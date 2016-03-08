---
title: "Code for edge cases"
author: hugo-giraudel
layout: post
---

Lately I have been writing tests for a large JavaScript project. When testing helper functions, it occurred to me developers usually write the code for the perfect scenario without considering anything that could go wrong.

Let’s imagine with have a function to make a salad. This function expects an array of ingredients, put them together and return the result. In JavaScript, its simplest form would be:

{% highlight js %}
function makeSalad (ingredients) {
  return ingredients.join(', ');
}
{% endhighlight %}

Alright, we can now create a salad:

{% highlight js %}
var salad = makeSalad( ['lettuce', 'tomatoes', 'sauce'] );
// -> "lettuce, tomatoes, sauce"
{% endhighlight %}

Very good. Now what happens if we call `makeSalad(..)` without any ingredients? Or if we call `makeSalad(..)` with something else than ingredients? Well let’s see.

{% highlight js %}
var salad = makeSalad();
// -> Error: ingredients.join is not a function
{% endhighlight %}

Indeed. Because we did not pass `ingredients`, its value is `undefined` which does not have a `join(..)` function. A simple fix would be to set a default value for `ingredients`.

{% highlight js %}
// ES6
function makeSalad (ingredients = []) {
  return ingredients.join(', ');
}

// ES5
function makeSalad (ingredients) {
  ingredients = ingredients || [];
  return ingredients.join(', ');
}
{% endhighlight %}

Now we can safely make our salad without passing any ingredients without risking a script crash. It will return an empty string. Given that the function usually returns a string, that seems like a valid way of handling this edge case.

{% highlight js %}
var salad = makeSalad();
// -> ""
{% endhighlight %}

Now what about the case where we pass something else than an array?

{% highlight js %}
var salad = makeSalad({
  lettuce: 'a lot',
  tomatoes: 2,
  sauce: '3 tea spoons'
});
// -> Error: Cannot read property 'join' of undefined
{% endhighlight %}

Again, `ingredients` is an object and an object does not have a `join(..)` function. To prevent this code from failing, we could add an extra check to our function to make sure that the given arguments is an array.

{% highlight js %}
function makeSalad (ingredients) {
  ingredients = ingredients || [];

  if (!Array.isArray(ingredients)) {
    throw new TypeError('`ingredients` parameter should be an array.');
  }

  return ingredients.join(', ');
}
{% endhighlight %}

In this case, the script will still throw an error but the error will be very explicit, which is much better for catching and debugging.

{% highlight js %}
var salad = makeSalad({
  lettuce: 'a lot',
  tomatoes: 2,
  sauce: '3 tea spoons'
});
// -> TypeError: `ingredients` parameter should be an array.
{% endhighlight %}

Long story short: handle edge cases in your functions. Don’t assume anything, and provide meaningful fallbacks and errors.
