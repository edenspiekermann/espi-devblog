---
title: 'Performance Profiling with CraftCMS'
author: dimitri-steinel
layout: post
mainImage: ""
mainImageAlt: ""
--- 

Are you encountering slow page-speed with CraftCMS? This guide will help you to identify the bottlenecks of your code. 

### Yii Debug Toolbar
Craft can help you to debug your templates with the build-in `Yii Debug Toolbar`.  This toolbar provides you with valuable informations about the database queries executed by the page. You can filter or sort all queries to see which one takes more time than expected.
<br><br>
> The [Yii Debug Toolbar](https://www.yiiframework.com/extension/yii-debug-toolbar) is a configurable set of panels that display various debug information about the current request/response and when clicked, display more details about the panel's content.

### Enable Debugger Toolbar
To enable the debugger toolbar, you need to go to your admin panel and click on the bottom left corner on the craft licence link. This will open up the licence overview. In order to enable the debugger toolbar, you need to have the full version of CraftCMS. There are 2 options:
<br>
* You buy it
* You run it locally or on a test environment. [Here is how](https://craftcms.com/support/try-craft-pro)
![Enable Craft Pro Vesion](http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_512/v1532351330/Espi/blog/performance%20profiling/perfomance-profiling__craft-pro-version.png)

In the admin panel, click on the top left corner and open up your profile. Check if you have **admin permission**. Then go to preferences tab and enable both checkboxes. Now you will see a small toolbar in the bottom right corner. Click on it to see more details.
![Enable Craft Toolbar](http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_512/v1532351329/Espi/blog/performance%20profiling/enable-debugger-toolbar.gif)

### Debugging your page
For debugging the page-speed, the most important information in the debugger toolbar are: 
<br>
* **Time**. Shows you how long the website takes to load 
* **DB**. Shows how many Database queries and the time they need

If you click anywhere in the toolbar, you can see all the details.
**Sort by duration** is the main feature we are using here. As a quick reminder, we want to identify queries, which take too much time and need some improvements. If you open up the **Performance Profiling** window, you can identify which part of the template takes the longest. 
![Craft Toolbar Performance Profiling](http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_512/v1532351330/Espi/blog/performance%20profiling/perfomance-profiling__debugger-toolbar__pp.png)

If you split up your templates in several different templates, it is easier to determine slow parts in the code. You can see in the image above, that the `global-header`  is part of the `layout` template. So in this case the `layout.twig` template is including the partial `global-header.twig`. You can read more about `includes` in the [Twig Docs](https://twig.symfony.com/doc/2.x/tags/include.html)

If your code is not split up like in the example above or if you need to profile your twig template more in detail, check out the [craft-twigprofiler](https://github.com/nystudio107/craft-twigprofiler). With this tool you can test smaller chunks of your template.

### Reduce database queries with Eager Loading
To keep the database queries as low as possible, you need to bundle the queries into bigger pieces and fetch as many related datas as you can at once.  In order to tell Craft in advance which data we are going to use, we need to **Eager Load** them.
With Eager Loading, Craft is able to return related elements. Like this Craft fetches sub-elements in advance instead of doing the same query for every sub-element over and over again.

A relation in craft has a source and a target. The source has a relation field and selects the other element. The target is selected by the source.
[Relations | Craft 2 Documentation](https://docs.craftcms.com/v2/relations.html#terminology)
> It’s important to grasp those terms, as they are relevant to the templating side of things


In Craft, [elements](https://docs.craftcms.com/v2/plugins/working-with-elements.html) are:  
* Assets
* Categories
* Entries
* Global Sets
* Matrix Blocks
* Tags
* Users 
<br><br>

It takes some steps to implement Eager Loading.
This example shows you how you Eager Load an imageField:

#### No Eager Loading
{% highlight twig %}
  {% raw %}
    {% set entries = craft.entries().section('info').all() %}

    {% for entry in entries %}
      {% set image = entry.imageField.one()%}
      {% if image %}
        <img src="{{ image.url }}" alt="{{ image.title }}">
      {% endif %}
    {% endfor %}
  {% endraw %}
{% endhighlight %}

#### With Eager Loading
{% highlight twig %}
  {% raw %}
    {% set 
      entries = craft.entries()
      .section('info') // 1. fetch entries from info section
      .with(['imageField']) // 2. what should be Eager Loaded?
      .all() 
    %} 

    {% for entry in entries %}
      {% set image = entry.imageField[0] %} // 3. fetch the image
      {% if image %}
        <img src="{{ image.url }}" alt="{{ image.title }}">
      {% endif %}
    {% endfor %}
  {% endraw %}
{% endhighlight %}

There are 2 major things you will notice:
* While selecting all entries from a section (`info`), you need to tell Craft, which fields you are going to use. In this case, we will need the `imageField`. Please note, that this can be a comma separated list (Array syntax) and you can have more than one field here.
* We also need to change the image selector from `.one()` to `[0]`

With Eager Loading, no matter how many entries you have to fetch, Craft only needs 3 database queries instead of N+1 (`N` = number of entries + `1` = initial entries query).

* Make sure you also read through the [Craft 3 Documentation](https://docs.craftcms.com/v3/dev/eager-loading-elements.html#eager-loading-image-transform-indexes)
* If you want to dive deeper, here is a really good & detailed read about [Eager loading](https://nystudio107.com/blog/speed-up-your-craft-cms-templates-with-eager-loading).

### Conclusion
Well of course this heavily depends on the page you are serving. But in general it is a good idea to have  the first contentful paint (FCP) below 2s. If you are higher than that, does not mean you are serving a bad website, but this means that your user will lose interest very quickly. As you can see in the illustration, the bounce rate increases slowly after 2 seconds and rapidly above 3 seconds.

[![Does Page Load Time Really Affect Bounce Rate?](http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_512/v1532351329/Espi/blog/performance%20profiling/relation__page-speed--bounce-rate.png)](https://royal.pingdom.com/2018/01/18/page-load-time-really-affect-bounce-rate/)

If you encounter that your page is having some performance issues, Eager Loading elements will improve the speed of your website. 
After you optimised your template, the next step would be to enable caching. Craft has its build-in [caching tag](https://docs.craftcms.com/v3/dev/tags/cache.html) with which you can cache specific parts of your template.
The [Blitz Plugin](https://github.com/putyourlightson/craft-blitz) is a intelligent static file caching which will improve the first contentful paint even more.

Do not only check your page-speed with the Craft debugger. Also check the network tab in your [browser inspector](https://developers.google.com/web/tools/chrome-devtools/network-performance/) and also use tools like these one:
* [WebPageTest](https://www.webpagetest.org/)
* [Google Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/)
* [Google Chrome Lighthouse plugin](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)
* [Pingdom Website Speed Test](https://tools.pingdom.com/)
