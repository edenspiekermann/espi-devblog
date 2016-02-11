---
title: "Introducing the Accessible Modal Dialog"
author: hugo-giraudel
layout: post
---

If there is one thing I try to push forward more and more at Edenspiekermann, it is accessibility. I can’t help but feeling that we do not care enough about assistive technology users. That’s a shame, really. It’s not that we don’t to do so, it’s more like we don’t really know where to start.

Almost all projects involve some form of modal dialog at one point or the other. Even recently, I implemented a small one page site which included a modal for the registration form. I will not express myself on how bad I think this UX pattern is as it is out of the scope of this article. In any case, accessible modal are hard. Very hard.

Fortunately, there is a super clever guy named [Greg Kraus](https://github.com/gdkraus) who implemented an accessible modal dialog a few years ago and [open-sourced it on GitHub](https://github.com/gdkraus/accessible-modal-dialog). Now that’s nice!

However, his version —no matter how good it is— requires jQuery. We try to avoid using jQuery as much as we can here. We realised we did not really need it most of the time. On top of that, his script is not very flexible: only one modal per page, hard-coded IDs inside the functions. Not very practical and certainly not a drop-in script for any project.

So I rolled up my sleeves and [improved it](https://github.com/edenspiekermann/accessible-modal-dialog).

## What’s up?

I spent a few hours improving the original version, and came up with new ideas as I was going so here is the full list.

The script no longer has jQuery as a dependency. It is now pure vanilla JS, ready to be used in any kind of project. You can still use jQuery if you want, but the script itself does not need it.

It is now possible to use several different modals on the same page, thanks to the [Factory pattern](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#factorypatternjavascript).

{% highlight js %}
// Get the modal element (with the accessor method you want)
var modalEl = document.getElementById('my-awesome-modal');

// Instanciate a new Modal module
var modal = new Modal(modalEl);
{% endhighlight %}

I added a small DOM API to open and close modals (`data-modal-show` and `data-modal-hide`) without having to bind any listener yourself. You can have as many openers and closers per modal as you want; no limit!

{% highlight html %}
<button data-modal-show="my-awesome-modal" type="button">
  Open the modal
</button>
{% endhighlight %}

{% highlight html %}
<button data-modal-hide type="button" title="Close the modal">
  &times;
</button>
{% endhighlight %}

There is also a teeny tiny JS API to be able to manually hide and show a modal from its instance. It’s also possible to know whether a modal is hidden or shown without having to query the DOM.

{% highlight js %}
// Provided `modal` contains a `Modal` instance

if (modal.shown) {
  modal.hide();
} else {
  modal.show();
}
{% endhighlight %}

And last but not least, I fixed a minor bug with the original version where elements with `tabindex` attribute set to `-1` would be focusable.

## How to use it?

As of today, the script is published through [npm](https://www.npmjs.com/package/accessible-modal-dialog). 

{% highlight bash %}
npm install accessible-modal-dialog --save
{% endhighlight %}

At this point, the script will be in your `node_modules` folder. Depending on your build step, this may vary. But you can probably just copy it in your assets folder like so:

{% highlight bash %}
cp node_modules/accessible-modal-dialog/accessible-modal-dialog.js assets/js/vendor
{% endhighlight %}

## What’s next?

Ideally, we would like the original author to merge our version in his repository (there is an [open issue for this](https://github.com/gdkraus/accessible-modal-dialog/issues/11)). There is no need to have 2 versions out there. Unfortunately, Greg Kraus seems to have stopped maintaining the initial repository, so for now we keep it on our side as well.

In any case, we are always looking for new ideas to improve our work so if you find a bug or want to suggest a feature, be sure to do so on the [GitHub repository](https://github.com/edenspiekermann/accessible-modal-dialog)!
