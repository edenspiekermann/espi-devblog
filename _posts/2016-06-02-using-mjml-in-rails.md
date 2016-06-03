---
title: "Using MJML in Rails"
author: hugo-giraudel
layout: post
--- 

Recently, I had to implement an email for the [Amaphiko](http://amaphiko.redbull.com) project. And since I won’t code email, at least not in this life, I had to find a workaround. There came [MJML](https://mjml.io/).

MJML introduces itself as “the responsive email framework”. Practically speaking, MJML is a markup language built on [React](https://facebook.github.io/react/) that produces email-compliant (whatever that means) markup once compiled.

MJML comes with about [20 components](https://mjml.io/documentation/#standard-components) to build simple newsletters (texts, images, links, buttons, columns, tables, etc.) and can be extended by leveraging the power of React for advanced usage.

## Setting up MJML in Rails

Amaphiko is a [Rails](http://rubyonrails.org/) app. Therefore, I needed a way to integrate MJML in the Rails pipeline to be able to produce proper responsive emails based on dynamic data.

While MJML is fairly recent, I was pleased to see there already is a fresh gem to plug it into Rails, called [mjml-rails](https://github.com/sighmon/mjml-rails).

First step would be to add it to the Gemfile. Beware though, the gem needs to required from within the Gemfile itself to work properly. Not quite usual but that’s how it work.

{% highlight bash %}
gem 'mjml-rails', '~> 2.1.1', require: 'mjml'
{% endhighlight %}

The thing is, this gem is basically just a wrapper around the [`mjml` npm package](https://www.npmjs.com/package/mjml). Because of this, it needs `mjml` to be installed independently. You can either install it globally to avoid adding a Node.js dependency to your project:

{% highlight bash %}
npm install -g mjml
{% endhighlight %}

Or if you already use some Node.js modules, you could also just add it to your `package.json` file:

{% highlight bash %}
npm install --save mjml
{% endhighlight %}

## Building a MJML-powered email

Let’s create a very simple email through MJML to illustrate this article.

First thing, in `app/mailers`, we create a `foobar_mailer.rb` file:

{% highlight ruby %}
# ./app/mailers/foobar_mailer.rb

class FoobarMailer < ActionMailer::Base
  def baz(name)
    @name = name

    mail(to: 'test@example.com', subject: 'test') do |format|
      format.mjml
    end
  end
end
{% endhighlight %}

Then in `app/views`, we create a `foobar_mailer` folder, containing a file named after our action `baz`. This file has to be a `.mjml` file.

{% highlight erb %}
<!-- ./app/views/foobar_mailer/baz.mjml -->

<mjml>
  <mj-body>
    <mj-container>
      <mj-text>Hello <%= @name %></mj-text>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

That’s it! 🎉

## Rendering a MJML partial

When the mail gets a bit long or repetitive, it might be interesting to leverage the power of ERB to have separate files, and render them inside the main template. For instance, each section of the newsletter could live in its own file.

Consider the following header (taken from [MJML documentation](https://mjml.io/documentation/#mjml-hero)):

{% highlight html %}
<!-- ./app/views/foobar_mailer/_foobar_header.mjml -->

<mj-hero
  mode="fixed-height"
  height="469px"
  background-width="600px"
  background-height="469px"
  background-url="https://cloud.githubusercontent.com/assets/1830348/15354890/1442159a-1cf0-11e6-92b1-b861dadf1750.jpg"
  background-color="#2a3448"
  padding="100px 0px">

  <mj-hero-content width="100%">

    <mj-text
      padding="20px"
      color="#ffffff"
      font-family="Helvetica"
      align="center"
      font-size="45"
      line-height="45"
      font-weight="900">
      GO TO SPACE
    </mj-text>

    <mj-button href="https://mjml.io/" align="center">
      ORDER YOUR TICKET NOW
    </mj-button>

  </mj-hero-content>

</mj-hero>
{% endhighlight %}

Then in the mail template:

{% highlight erb %}
<!-- ./app/views/foobar_mailer/baz.mjml -->

<mjml>
  <mj-body>
    <mj-container>
      <!-- Email header -->
      <%= render partial: 'foobar_header', formats: [:html] %>
  
      <!-- Email content -->
      <mj-text>Hello <%= @name %></mj-text>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

Note that:

* the leading underscore is not necessary;
* the extension can be safely omitted;
* the `formats` property has to be set to `[:html]` (not `[:mjml]`).

## Wrapping things up

MJML is such a fantastic tool to build responsive email newsletters without the hassle and I am super happy to see a proper and simple Rails integration.

No longer will you struggle integrating a newsletter! 