---
title: 'Improve pagespeed: Lazy Loading Images'
author: dimitri-steinel
layout: post
mainImage: ""
mainImageAlt: ""
--- 

Don't want to read it all? [Here’s the code](#snippet)

### First meaningful paint

Have you heard of first meaningful paint? No? You should! The first meaningful paint is a very important parameter of your website. The longer a page needs to load the more likely as user is to lose interest and leave your website. So there is a very close connection between page speed and bounce rate.

So what we want to achieve is to give the user a visible result as early as possible. There are many things you can do for speeding up the first meaningful paint. People talk about critical CSS, tree-shaking JavaScript and brotli compression because gzip is not compressing it enough. They talk about avoiding the use of libraries and help you to optimize your code to make it as small as possible. And they are right. All those things are great improvements to page speed, and a good page speed means more users will enjoy you website. But before we dive deeper into these topics, we start with optimizing images. There is nothing else which will improve your pagespeed more with that little effort.

### Optimizing images
Images should only be loaded in the size the users really need, meaning that you don’t need to load an image with the resolution of 3000x2000 when you only need to show it in a teaser size of 500x333. You can do the math how many data ressources you will save by just resizing the image and download it in only the size you really need it.
<br/>
<br/>
After resizing the image the next important step is to minify the image with an image compressor. We use [OptiImage](https://getoptimage.com/) or [ImageOptim](https://imageoptim.com/mac) for that. Take care that you do not minify too much. There is a chance that you will make the image look not nice anymore (blurry/unsharp edges or awful color gradients). By optimizing the images with those tools instead of photoshop or something similar, you can save a lot of bytes. Well done! But now we download and render all images on our page even though we do not see them.
![Image Optim](https://res.cloudinary.com/dsteinel/image/upload/v1524256663/imageoptim.png)


### Lazy loading images
Lazy loading means that you only load the images which are actually visible in your viewport. So if the users land on your website they don’t need to load the images which are at the very bottom of your page. What we want to do is only show the user the image which is in the viewport.

First we need some images in the DOM, so let’s create some dummy content.
Make sure you apply some styling so that you need to scroll on the page and you do not show all images above the fold (in the current viewport).

{% highlight html %}
<div class="image-container">
  <ul class="image__list">
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg"
        alt="blue lake with surrounding mountains">
    </li>
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796305/pexels-photo-104336.jpg"
        alt="street in the middle of a forrest">
    </li>
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796376/les-anderson-175603.jpg"
        alt="two elder people opening togehter a christmas present">
    </li>
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796381/pexels-photo-737108.jpg"
        alt="black and white clothes hanging on a clothes rail">
    </li>
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796475/tobias-van-schneider-122288.jpg"
        alt="book collection photographed from top">
    </li>
    <li class="image__list-item">
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg"
        alt="brown meadow with a tower in the very back">
    </li>
  </ul>
</div>
{% endhighlight %}

{% highlight css %}
.image-container {
  margin: 0 auto;
  width: 450px;
}

.image__list-lazy-image { 
  display: none;
}

.image__list-image {
  display: block;
}

.image__list-item {
  margin-bottom: 40px;
}
{% endhighlight %}

Let’s get to JavaScript part. There is a very nice little helper called `Intersection Observer` which was announced on the 14 September’17 by Google. They describe it as follows:
> "This specification describes an API that can be used to understand the visibility and position of DOM elements ("targets") relative to a containing element or to the top-level viewport ("root". > The position is delivered asynchronously and is useful for understanding the visibility of elements and implementing pre-loading and deferred loading of DOM content.” 
[source](https://www.w3.org/TR/intersection-observer/)

**This is just perfect for lazy loading images!**

So we first have to gather all the images we marked above as “lazy-images”. Those are the images that have a data attribute containing our image source. Make sure that the image has no `src` attribute set, otherwise, the browser will load an image before the IntersectionObserver can do its magic. The logic result we are now trying to achieve is that no lazy load image has an image source, meaning that no image will be loaded initially. The imageObserver will now check on each image, if it is in the viewport. If it is, then we will replace the content of the src attribute with the content of the data-src attribute (the actual image source). If this is done, we will download and display the image.

{% highlight JavaScript %}
// rootMargin increases the virtual size of your image (you can not see it, but the IntersectionObserver does)
// uses syntax of CSS margin properties (top right bottom left). Always use px here.
const options = {
  rootMargin: '0px 0px 250px 0px'
}

// takes a callback as first parameter and options as second
const observer = new IntersectionObserver(lazyLoader, options);

// select all our image which we want to have lazy loaded
document.querySelectorAll('.image__list-lazy-image').forEach(image => {
  // put them in the observer
  observer.observe(image);
});

function lazyLoader (entries) {
  // loop through all images
  entries.forEach(entry => {
    // does the viewport hit the current image
    if (entry.isIntersecting) {
      // yes! load the image
      lazyLoadImage(entry);
    }
  });
}

function lazyLoadImage (observedImage) {
  /**
  *  We hit an observed image!
  *  First, remove the lazy loading class. This will show the image
  *  Then remove the observer
  */
  observedImage.target.classList.remove('image__list-lazy-image');
  
  // does it have a data attribute with image-src?
  if (observedImage.target.getAttribute('data-src')) {
    // yes! So lets make the image source equal to the data image source and render it!
    observedImage.target.src = observedImage.target.getAttribute('data-src');

    // now that the image has been rendered, we don't need to observe it anymore
    observer.unobserve(observedImage.target);
    // image has hit the lowest part of the viewport, so we can show it now
    observedImage.target.classList.remove('image__list-lazy-image');
  }
}
{% endhighlight %}

### Polyfill
As you can see [here](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API), the browser support is quite good, but not yet good enough for us. If you also want to improve the browser support, then follow the instructions on [this npm module](https://github.com/w3c/IntersectionObserver/tree/master/polyfill). If you are still unsure, you can wrap the start of our observer in an if-condition to check if the IntersectionObserver is supported:

{% highlight JavaScript %}
  if('IntersectionObserver' in window) {
    // select all our image which we want to have lazy loaded
    document.querySelectorAll('.image__list-lazy-image').forEach(image => {
      // put them in the observer
      observer.observe(image);
    });
  } else {
    // maybe you want to do something else if we don't have IntersectionObserver support
  }
{% endhighlight %}

### No JavaScript fallback
If for any reason the user has not enabled JavaScript or the JavaScript throws an error and can not be executed, ALL images in your website will not be shown, because all of them rely on the JavaScript which manipulates our image source.
Here is a solution with a `noscript` tag. The `noscript` tag will only render when scripting is **not** active. So, if JavaScript is not active, the browser will show our images with no lazy loading. Our other images (the lazy loading images) with no valid source will have `display: none`. 

{% highlight html %}
<noscript>
  <style>
    .image__list-lazy-image { 
      display: none;
    }
  </style>
</noscript>
<div class="image-container">
  <ul class="image__list">
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg" alt="blue lake with surrounding mountains">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg" alt="blue lake with surrounding mountains">
    </li>
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796305/pexels-photo-104336.jpg" alt="street in the middle of a forrest">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796305/pexels-photo-104336.jpg" alt="street in the middle of a forrest">
    </li>
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796376/les-anderson-175603.jpg" alt="two elder people opening togehter a christmas present">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796376/les-anderson-175603.jpg" alt="two elder people opening togehter a christmas present">
    </li>
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796381/pexels-photo-737108.jpg" alt="black and white clothes hanging on a clothes rail">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796381/pexels-photo-737108.jpg" alt="black and white clothes hanging on a clothes rail">
    </li>
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796475/tobias-van-schneider-122288.jpg" alt="book collection photographed from top">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796475/tobias-van-schneider-122288.jpg" alt="book collection photographed from top">
    </li>
    <li class="image__list-item">
      <noscript>
        <img class="image__list-image" src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg" alt="brown meadow with a tower in the very back">
      </noscript>
      <img class="image__list-image image__list-lazy-image" data-src="http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_450/v1523796300/pexels-photo-559768.jpg" alt="brown meadow with a tower in the very back">
    </li>
  </ul>
</div>
{% endhighlight %}

<div id="snippet"></div>

[Codepen Example](https://codepen.io/dSteinel/pen/mxYZdp).