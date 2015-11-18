---
layout: post
author: eric-schaefer
title: Ensuring Component Stability through Prop Validation
---

Our React application for [Amaphiko](https://amaphiko.redbull.com) has grown quite a lot over the last year. One of our primary goals is to ensure the scalability and stability of the application. And to that end, we’ve been religiously adding prop checks to the top of every component. This serves a few purposes:

### 1. Immediately see what data a component can process

When you’re looking at the code for a component, the first thing you expect to see is what type of data you have available. This eliminates needing to switch between looking at the API documentation and your component code. `propTypes` can serve as a sort of mini-reference to your back-end’s API.

{% highlight js %}
import React from 'react';

const ProjectTile = React.createClass({

  propTypes: {
    project: React.PropTypes.shape({
      id: React.PropTypes.number.isRequired,
      slug: React.PropTypes.string.isRequired,
      title: React.PropTypes.string.isRequired,
      state: React.PropTypes.string.isRequired,
      created_at: React.PropTypes.string.isRequired
    }).isRequired
  },

  render() { … }

})
{% endhighlight %}

Immediately we can see that this `ProjectTile` component accepts a `project` object. And we also see its properties, like `id` and `title`. So when we’re writing the UI elements inside the component, we already know we can call properties like `this.props.title` in our render method, without having to look at the parent component or some other documentation.

### 2. Get console warnings if a component receives an incorrect or missing data type

If a prop is missing, or has an incorrect data type, you’ll see a warning in the JavaScript console. And for [performance reasons](https://facebook.github.io/react/docs/reusable-components.html), React will only check the `propTypes` in development mode. One can start to see the power of this when combining these prop checks with integration tests and other `pre-push` [hooks](https://github.com/maxhoffmann/captain-git-hook).

### 3. See if API data changed

It is often the case as a project grows, that the structure of a back-end API response could change, and therefore break an element in the UI if that piece of data is missing, or if a new property is added. Having `propTypes` can eliminate a whole swath of these kinds of errors.

Let’s consider the code snippet above. If a new property `teaser` is added to `this.props.project`, the required object shape defined in `React.PropTypes.shape()` would not match. In development, the console would warn us of this, and we would know to re-examine the data we’re getting from `this.props`, and update our prop checks accordingly.

### 4. Get one step closer to static type checks

JavaScript is an extremely expressive language, and ES6 even more so. But sorely, enforcing types in JS is tricky business, and introduces notoriously difficult debugging edge cases since the language is weakly typed. Religious use of `propTypes` can really minimize these edge cases. Combined with a an additional type checking tool like [Flow](http://flowtype.org), prop checks can drastically improve your long-term productivity and coerce your code to seem more strongly typed.

### Takeaway

The component architecture React applications is extraordinarily conducive to stronger type checking, and React’s built-in `propTypes` makes it even more so. There is really no excuse for not using this functionality, although you certainly could. Omit it at your own peril if your application needs to scale.

Empirical evidence from [Kleinschmager, Hanenberg and others](http://pleiad.dcc.uchile.cl/papers/2012/kleinschmagerAl-icpc2012.pdf) shows that static type checking had a positive impact on six of nine programming tests in their research. It’s no longer just a programming trope to claim that stronger type checking leads to more maintainable software. So for a weakly typed language like JavaScript, we are served well to enforce type checks in other ways, within the architecture of our applications.

