---
title: "Stranger Forms: Textarea Auto Resize with React"
author: eric-schaefer
layout: post
---

![I freaking love this show.](https://cloud.githubusercontent.com/assets/547148/18024962/171ce86c-6bce-11e6-8216-5ab7f361fdde.jpg)

_tldr:_ [here’s the code](#snippet) if that’s what you’re here for.

Forgive the shameless _Stranger Things_ fandom, but there’s a good metaphor in there. The “upside down” is a world that mirrors our own, but it’s hidden from our view, and it’s way damn spookier.

Well, to get tricky form elements to do what we want, we’re gonna need to go there.

Recently I wanted to make a `<textarea>` element automatically expand vertically as text is entered. A basic `<div>` element would do this, but form elements are a different animal. For example, watch what happens as a word breaks onto the next line:

<video class="video--example" src="/assets/video/posts/upside-down-1.mp4" autoplay loop>
</video>

The `<textarea>` doesn’t expand to fit the content inside. This can get pretty annoying if you’re trying to keep track of your writing. There is no pure CSS solution. If you’re a masochist you can stop what you’re doing and resize it manually…

<video class="video--example" src="/assets/video/posts/upside-down-2.mp4" autoplay loop>
</video>

But it’s 2016 and that seems insane. Just fixing the element to a static height is not an option either. The intended behavior that I have in mind is more like this:

<video class="video--example" src="/assets/video/posts/upside-down-3.mp4" autoplay loop>
</video>

So how do we get there? Well, we’re going to build a ghost `<div>` that mirrors the contents of the `<textarea>`. Then when the hidden `<div>` resizes, we apply that height to the `<textarea>`. And to make sure the hidden element is not problematic for assistive technology, we add `aria-hidden="true"`.

<video class="video--example" src="/assets/video/posts/upside-down-4.mp4" autoplay loop>
</video>

It’s not the most original idea, but I figured that an implementation in React is probably useful for some people. The barebones component is in a Gist below.

<div id="snippet"></div>

Or [get a full example in JSFiddle](https://jsfiddle.net/708fp9t2/22/).

<script src="https://gist.github.com/eschaefer/a0da539b19ac0f7765fa73216a3bf1b0.js"></script>
