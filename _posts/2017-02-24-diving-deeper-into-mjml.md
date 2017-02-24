---
title: 'Diving Deeper into MJML'
author: zhuo-fei-hui
layout: post
--- 

Last year, our colleague [Hugo Giraudel](http://hugogiraudel.com/) wrote a blog post about [integrating MJML into Rails](http://dev.edenspiekermann.com/2016/06/02/using-mjml-in-rails/). After working with it for a while in some of our other projects and with the recent upgrade of the framework to version 3, we thought that it's time for an update!

## Use MJML in a Rails Email Layout Setup

If you have different kinds of emails in your project, you might want to extract some of the shared logic into something you could reuse. In Rails this is done through the Action Mailer Layouts. To do this you have to create a layout file and use `yield` to render your partial inside the layout: 

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

Now you add this layout to your mailer class:

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

All you have left now is to remove the shared markups from your partial and you are done! 
{% highlight erb %}
<!--  ./app/views/user_mailer/welcome_email.mjml.erb -->
<mj-section>
  <mj-column>
    <mj-text> Hello <%= @name %></mj-text>
  </mj-column>
</mj-section>
{% endhighlight %}

Pay attention to the changes to the file formats, it otherwise won't work. 

## Style Your MJML Templates

Creating email templates for the same project also means applying similar stylings to each of the templates.

With the last upgrade of MJML to version 3 it became a lot easier to reuse some of the stylings. Within the `mj-attributes` tag you can define global stylings with `mj-all`, create styling classes with `mj-class` or set the styling for a specific tag by just mentioning it. 

You can set 
{% highlight erb %}
<!--  ./app/views/layouts/mjml_email.mjml -->
<mjml>
  <mj-head>
    <mj-attributes>
      <mj-all font-family="Arial" align="center"/>
      <mj-class name="big" font-size="20px" />
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
                * with a font-size of 20px
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

Another way to apply styling is to use `mj-style` and write plain css in it. You can then use `mj-raw` to write plain html and use your css class: 

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

## Use Web Font 

There are several ways of integrating web fonts into your emails. [Campaign Monitor](https://www.campaignmonitor.com/resources/guides/web-fonts-in-email/) has a good introduction on that. MJML introduced the `mj-font` tag to allow customized fonts, which should work out in most of the cases:
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

But in one of our projects, we are loading some fonts, which are nested in the Rails assets folder structure with `@font-face`. To allow this, we had to hack MJML a little bit, since couldn't find anything which would allow it. 

{% highlight erb %}
<mjml>
  <mj-head>
    <mj-title>MyWings - Red Bull</mj-title>
    <mj-font name="Oswald" href="https://fonts.googleapis.com/css?family=Oswald" />
    <mj-attributes>
      <mj-all font-family="FSBlakeLight, Oswald, Arial"/>
    </mj-attributes>
  </mj-head>
  
  <mj-body>
    <mj-container>
      <mj-raw>
        <!-- This is a bit of a hack! -->
          @font-face {
            font-family: 'FSBlakeLight';
            src: url("<%= asset_path('blake_light/fs_blake_web-light.eot') %>");
            src: url("<%= asset_path('blake_light/fs_blake_web-light.woff') %>") format('woff'), url("<%= asset_path('blake_light/fs_blake_web-light.ttf') %>") format('truetype'), url("<%= asset_path('blake_light/fs_blake_web-light.svg#FSBlake-Light') %>") format('svg');
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

We are still super happy with MJML and are trying to integrate it into more of our projects. 

The version 3 upgrade of MJML helps a lot in terms of DRY up the code and to integrate standard styling elements. But when the design becomes more complex, we did reach some of the limits, but the lively discussions and issue requests online promise that new features are soon to come. 

We test our emails with [Litmus](https://litmus.com/), which allows us to see how our emails get rendered on different devices with different email clients. Since the switch to MJML we haven't been experiencing any broken stylings or missing elements. 

## Sources

* ["Rails Guides: Action Mailer Basics"](http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-layouts)
* ["Using MJML in Rails" by Hugo Giraudel](http://dev.edenspiekermann.com/2016/06/02/using-mjml-in-rails/)
* ["MJML Reaches Another Level with the Release of v3" by Nicolas Garnier](https://www.google.de/search?q=MJML+reaches+another+level+with+the+release+of+v3&oq=MJML+reaches+another+level+with+the+release+of+v3&aqs=chrome..69i57j69i60l2.368j0j1)
* [Official list of MJML packages on GitHub](https://github.com/mjmlio/mjml/tree/master/packages)
* ["All you need to know about web fonts in email" by Campaign Monitor](https://www.campaignmonitor.com/resources/guides/web-fonts-in-email/)