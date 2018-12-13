---
title: 'Diving Deeper into MJML'
author: zhuo-fei-hui
layout: post
--- 

Last year, our colleague [Hugo Giraudel](http://hugogiraudel.com/) wrote a blog post about [integrating MJML into Rails](http://dev.edenspiekermann.com/2016/06/02/using-mjml-in-rails/). After working with it for a while in some of our other projects and with the recent upgrade of the language to version 3, we thought that it's time for an update!

## Use MJML in a Rails Email Layout Setup

If you have different kinds of emails in your project, you might want to extract some of the shared logic into something you could reuse ("Don't Repeat Yourself!"). In Rails this is done through the [Action Mailer Layouts](http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-layouts). To do this you have to create a layout file and use `yield` to render your partial inside the layout: 

{% highlight erb %}
<!--  ./app/views/layouts/mjml_email.mjml -->
<mjml>
  <mj-body>
    <mj-container>
      <%= yield %>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

Now you can add this layout to your mailer class:

{% highlight ruby %}
# ./app/mailers/user_mailer.rb
class UserMailer < ApplcationMailer::Base
  layout: 'mjml_email' # <- add this 
  
  def welcome_email(user)
    @name = user.name 
    
    mail(to: user.email) do |format|
      format.mjml 
    end
  end
end
{% endhighlight %}

All you have to do now is to remove the shared markups from your partial: 

{% highlight erb %}
<!--  ./app/views/user_mailer/welcome_email.mjml.erb -->
<mj-section>
  <mj-column>
    <mj-text> Hello <%= @name %></mj-text>
  </mj-column>
</mj-section>
{% endhighlight %}

Pay attention to the different file formats (when and where to use `.mjml` or `.mjml.erb`), otherwise it won't work! 

## Style Your MJML Templates

Creating email templates for the same project also means applying similar stylings to each of the templates. With the recent [upgrade of MJML to version 3](https://medium.com/mjml-making-responsive-email-easy/mjml-reaches-another-level-with-the-release-of-v3-945d0d988d97#.8fzdlb430) it became a lot easier to reuse some of the stylings. 

Within the `mj-attributes` tag you can now define global stylings with `mj-all`; create styling classes with `mj-class`; or set the styling for a specific tag by just mentioning it: 

{% highlight erb %}
<!--  ./app/views/layouts/mjml_email.mjml -->
<mjml>
  <mj-head>
    <mj-attributes>
      <mj-all font-family="Arial" align="center"/>
      <mj-class name="big" font-size="42px" />
      <mj-section background-color="#ffff00"/>
    <mj-attributes>
  </mj-head>
  
  <mj-body>
    <mj-container>
      <mj-section>
        <mj-text mj-class="big"> 
          <!-- This text will appear 
                * centered
                * in Arial 
                * with a font-size of 42px
                * on a bright yellow background 
                * 😎 
          -->
          Hello World! 
        </mj-text>
      </mj-section>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

Another way to apply styling is to use `mj-style` and write plain CSS in it. You can then use `mj-raw` to write plain html and apply the CSS class: 

{% highlight erb %}
<!--  ./app/views/layouts/mjml_email.mjml -->
<mjml>
  <mj-head>
    <mj-style>
      .card-shadow {
        margin-top: 1.6em;
        -webkit-box-shadow: 0px 0px 15px 5px rgba(220,220,220,0.5);
        -moz-box-shadow: 0px 0px 15px 5px rgba(220,220,220,0.5);
        box-shadow: 0px 0px 15px 5px rgba(220,220,220,0.5);
      }
    </mj-style>
  </mj-head>
  
  <mj-body>
    <mj-container>
      <mj-section>
        <mj-raw> 
          <!-- This adds some box-shadow to the image -->
          <img class="card-shadow" src="http://example.com/image.jpg"/> 
        </mj-raw>
      </mj-section>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

## Use Web Fonts 

There are several ways of integrating web fonts into your emails. [Campaign Monitor](https://www.campaignmonitor.com/resources/guides/web-fonts-in-email/) has a good introduction on that. MJML introduced the `mj-font` tag to allow customized fonts, which should be sufficient in most of the cases:
{% highlight erb %}
<mjml>
  <mj-head>
    <mj-font name="Oswald" href="https://fonts.googleapis.com/css?family=Oswald" />
  </mj-head>
  
  <mj-body>
    <mj-container>
      <mj-section>
        <mj-column>
          <mj-text font-family="Oswald, Arial">
            Hello World!
          </mj-text>
        </mj-column>
      </mj-section>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

But in one of our projects, we are loading fonts which are nested in the Rails assets folder structure `./app/assets/fonts/*` and we used to load it with `@font-face` into our email templates. 

To allow this again we had to hack MJML a bit, since we weren't able to find anything else to replace this. 

{% highlight erb %}
<mjml>
  <mj-head>
    <!-- mj-font gets converted into @import -->
    <mj-font name="Oswald" href="https://fonts.googleapis.com/css?family=Oswald" />
    <mj-attributes>
      <!-- This sets the global font of the template to FSBlakeLight and adds fallbacks. -->
      <mj-all font-family="FSBlakeLight, Oswald, Arial"/>
    </mj-attributes>
  </mj-head>
  
  <mj-body>
    <mj-container>
      <mj-raw>
        <!-- 
          This is a bit of a hack! 
          mj-raw only works within the mj-container tag. 
        -->
        <style>
          @font-face {
            font-family: 'FSBlakeLight';
            src: url("<%= asset_path('blake_light/fs_blake_web-light.eot') %>");
            src: url("<%= asset_path('blake_light/fs_blake_web-light.woff') %>") format('woff'), 
                 url("<%= asset_path('blake_light/fs_blake_web-light.ttf') %>") format('truetype'), 
                 url("<%= asset_path('blake_light/fs_blake_web-light.svg#FSBlake-Light') %>") format('svg');
            font-weight: normal;
            font-style: normal;
          }
        </style>
      </mj-raw>
      <%= yield %>
    </mj-container>
  </mj-body>
</mjml>
{% endhighlight %}

## Wrapping Up

We are still very happy with MJML and are trying to integrate it into more and more of our projects. 

The version 3 upgrade helps a lot in terms of DRY up the code and to integrate standard styling elements. But when the design becomes more complex, we did reach some of the limits of MJML. But the [lively discussions and issue requests online](https://github.com/mjmlio/mjml/issues) promise that new features are soon to come. 

We've been testing our emails with [Litmus](https://litmus.com/), a service which allows us to see how our emails get rendered on different devices with different email clients. Since the switch to MJML we've more or less stopped experiencing broken stylings or missing elements. 🤘

PS.: the [official list of MJML packages](https://github.com/mjmlio/mjml/tree/master/packages) also has been a great source of help. 