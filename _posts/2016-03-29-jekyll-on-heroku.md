---
title: "Running a Jekyll Site on Heroku, The Easier Way"
author: eric-schaefer
layout: post
---

Recently I’ve been working on a prototype where I simply needed to serve the static files compiled by [Jekyll](https://jekyllrb.com/), on Heroku. Seems straightforward right?

Wrong. Turns out that for every URL that isn’t just a single page on root `index.html`, you need to add a custom `config.ru` to your project with all kinds of `rack-rewrite` rules to remove the `.html` extensions from URLs, add a `Gemfile`, and the list goes on… just to get Heroku to serve static files with a `Rack` server.

Dave Rupert’s article on [deploying Jekyll to Heroku using Dropbox](http://daverupert.com/2015/02/jekyll-heroku-dropbox/) got me partially there, but it seemed to me like his solution could be simplified, and better yet, support nice clean URLs for more than one page.

So, here’s an improved approach to run a compiled Jekyll site on Heroku.

### Set your Heroku buildpack to `heroku/php`

```
$ heroku buildpacks:set heroku/php
```

Yeah, that’s right. The good ol’ PHP buildpack comes with an apache server that is more than capable of serving these stupid lil' static assets, and automatically handle the obvious case of rewriting `/contact-us/` to silently point at `/contact-us/index.html`.

Note: Remove any previous buildpacks that were auto-set on previous deployments.

### Add an empty `composer.json` file

The presence of this file is a signal for Heroku to initiate its PHP buildpack on deployment. In your project root:

```
$ echo '{}' > composer.json
```

### Add a `Procfile` to point at your public `_site` folder

[Procfiles](https://devcenter.heroku.com/articles/getting-started-with-php#define-a-procfile) are a Heroku-specific config that spin up workers and define little customizations on app deployment. In your project root:

```
$ echo 'web: vendor/bin/heroku-php-apache2 _site/' > Procfile
```

This should tell apache to serve up the generated assets in your `_site` folder.

## Summary

In short, we are accomplishing a quick way to serve static assets, and get the basic URL rewriting behavior that we expect from the folder structure of a Jekyll project. The automatic URL rewrites are what ultimately convinced me of using Heroku’s PHP buildpack in this scenario. Hopefully it can help some of you agonizing over Rack configuration.
