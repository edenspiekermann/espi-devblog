---
layout: post
author: stuart-whitehead
title: An introduction to regular expressions
---

Regular expressions, or regex, is a tool for testing, analysing and matching patterns of text in a larger text set. Regex theory was conceived by an American mathematician in 1956 and practical implementations became popular in the late 1960s. They have been around for a _very_ long time and are rooted in Computer Science theory.

Regex set theory defines the underlying standard, however different languages and environments implement different features and syntaxes to achieve it. I'll focus on Javascript's implementation because it is simpler than many, and we use it often.

[Regexr](http://www.regexr.com/) is perfect for experimenting and testing.

## Setting up camp

Regular expressions are often referred to as patterns, which are enclosed in delimiters. In JS, this is the forward slash. Chuck your patterns in between these.

{% highlight js %}
var regex = /my regex pattern/;
{% endhighlight %}

## Atoms

It's nice think of each logical unit of a regex pattern as an _atom_. This could be a single letter, or a complex sequence of characters—so long as they are interpreted as a single unit.

The simplest pattern is a string literal. A string literal will match the exact text in the target string.

{% highlight js %}
var regex = /I love dev couch/;
{% endhighlight %}

### Special characters

More complex patterns can be built using _special characters_ which represent a set of characters or escaped characters. This table show's the most common:

Special character | Representation
------------------------|---------------------
`\w` | alphanumeric character, or an underscore
`\W` | anything but an alphanumeric character or an underscore
`\d` | a numerical digital
`\D` | anything but a numerical digital
`\s` | a white space character
`\S` | anything but a white space character
`\b` | a word boundary
`\B` | a non-word boundary
`.` | any character except newline

### Character sets

String literals and special characters can be combined to create character sets. In JS, these are enclosed in square brackets.

{% highlight js %}
var regex = /[xyz]/; // matches x, y or z
{% endhighlight %}

The following table shows variations of sets:

Set pattern | Representation
---------------|---------------------
`[xyz]` | x, y or z
`[a-z]` | anything between a to z
`[a-zA-Z0-9_]` | a to z, A to Z, 0 to 9 or an underscore. Equivalent to `\w`
`[^a-z]` | anything _except_ a to z
`[\s\S]` | any whitespace character or any non-whitespace character. Essentially, anything!

### Qualifiers

String literals, special characters or character sets can be followed by a qualifier. This determines how many times the atom can be repeated.

{% highlight js %}
var regex = /\d{4}/; // matches 4 numerical digits
{% endhighlight %}

The following table describes common qualifiers:

Qualifier | Representation
------------|---------------------
`*` | zero or more
`+` | one or more
`?` | zero or one
`{x}` | exactly _x_
`{x, y}` | between _x_ and _y_

By default, qualifiers are greedy, meaning that they will try to match as many characters as possible. To make them non-greedy, append a `?` after the qualifier.

{% highlight js %}
var regex = /\d+/; // Matches 0123456789 from 0123456789dev
var regex = /\d+?/; // Matches 0 from 0123456789dev
{% endhighlight %}

## Expressions
Multiple atomic patterns can be combined to build up expressions. The simplest way to do this is to combine string literals, special characters, character sets and qualifiers into one pattern.

{% highlight js %}
var regex = /\d{4}-\d{2}-\d{2}/; // Matches 2015-09-01
{% endhighlight %}

### Sub-expressions and groups

To build even more complex patterns, expressions can be grouped into sub-expressions which themselves can have qualifiers.

{% highlight js %}
var regex = /(\d{2}-?){3}/ // Matches 12-34-56
{% endhighlight %}

The following table describes common grouping patterns:

Group | Representation
---------|---------------------
`()` | Capturing group
`(?:)` | Non-capturing group
`(?=)` | Positive lookahead
`(?!)` | Negative lookahead

It's possible to reuse captured groups in the same pattern with the `\n` special character (where _n_ is a positive integer). For example, this pattern will find any double occurrences of words, like ‘the the’ or ‘and and’:

{% highlight js %}
var regex = /(\w+) \1/; // Matches ‘the the’
{% endhighlight %}

## Reverse engineering
A _fun_ way to learn regex is to deconstruct someone else's pattern. The following regex pattern was taken from Mathias Byrens [‘In search of the perfect URL validation regex’](https://mathiasbynens.be/demo/url-regex)

{% highlight js %}
var regex = /(https?|ftp)://(-\.)?([^\s/?\.#-]+\.?)+(/[^\s]*)?$/;
{% endhighlight %}

This table breaks down each atom and describes its purpose:

Atom | Representation
-------|----------------------
`(https?|ftp)` | ‘http’, ‘https’ or ‘ftp’
`://` | string literal ‘://’
`(-\.)?` | ‘-.’ zero or one times
`([^\s/?\.#-]+\.?)+` | Anything but whitespace, ‘?’, ‘.’, ‘#’ or ‘-’ one or more times, with an optional ‘.’. This sub-expression can be matched one or more times
`(/[^\s]*)?` | A forward slash, followed by zero or more non-whitespace characters. This sub-expression is matched zero or one times
`$` | End of the line

An alternative way to visualise this regex comes from [Regexper](http://regexper.com/):
![visual-regex](https://cloud.githubusercontent.com/assets/4330008/9628274/116449c6-516a-11e5-8226-54f22d8344c0.png)

## Further reading

* [MDN Regular Expressions](https://developer.mozilla.org/en/docs/Web/JavaScript/Guide/Regular_Expressions) — The entry reference on the Mozilla Developer Network
* [Regexr](http://www.regexr.com/) — They've got a solid cheatsheet and a comprehensive reference
* [Learning Regular Expressions: The Practical Way](http://hugogiraudel.com/2015/08/19/learning-regular-expressions-the-practical-way/) — An introduction article by Hugo Giraudel
* [Rexegg](http://www.rexegg.com/) — A very good (and somewhat advanced) source of knowledge about regular expressions
* [Regexper](http://regexper.com/) — A diagram generator to visualise the logic behind a regular expression
