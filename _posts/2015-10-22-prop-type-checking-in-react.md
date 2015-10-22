---
layout: post
author: Eric Schaefer
title: Ensuring Component Stability Through Prop Validation
---

Our React application for [Amaphiko](https://amaphiko.redbull.com) has grown quite a lot over the last year. One of our primary goals is to ensure the scalability and stability of the application. And to that end, we've been religiously adding prop checks to the top of every component. This serves a few purposes:

### 1. Immediately see what data a component can process.

When you're glancing at the code for a component, the first thing you expect to see is what type of data you have available. This eliminates needing to switch between looking at the API documentation and your component code. `propTypes` can serve as a sort of mini-reference to your back-end's API.

```
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

  render() ...

```

Immediately we can see that this `ProjectTile` component accepts a `project` object. And we also see its properties, like the `id` and `title`. So when we're writing the UI elements inside the component, we already know we can call properties like `this.props.title` in our render method, without having to look at the parent component or some other documentation. Seems like a no-brainer for this reason alone.


### 2. Get console warnings if a component receives an incorrect or missing data type.


### 3. See if API data changed




