# The ESPI Developer Blog

Rumblings from the devs at Edenspiekermann. Front-end, back-end, deep-end.

### [http://dev.edenspiekermann.com](http://dev.edenspiekermann.com)


Run locally:

```
$ cd espi-devblog/
$ gem install jekyll
$ jekyll serve --config _config.yml,_config_dev.yml --watch
```

### How to publish an article

- Create a branch for your post, for example `post/setting-up-docker`
- Create a new markdown file in `/_posts/` with your article content. Follow the filename convention `YYYY-MM-DD-descriptive-slug-here.md`.
- Open a pull request and ping one of your friendly dev peers to quickly scan the article for typos.
- Merge `post/your-branch` >> `master` >> `gh-pages` to re-publish the blog. The final merge into `gh-pages` will trigger Github pages to re-publish automagically.

### About authors

To display a small block of content about the author at the end of an article, we rely on [a YAML file](https://github.com/edenspiekermann/espi-devblog/blob/master/_data/authors.yml) containing all information about all existing authors on the blog.

When publishing an article for you or one of your dev peers, make sure the author actually exists in this file. If it does not, add it. The key is the slugified name (later used in the YAML Front Matter of articles), and the value is an object with `name`, `description` and `image`. For the image, you can hotlink the one [from the website](http://www.edenspiekermann.com/people).

```yaml
hugo-giraudel:
  name: "Hugo Giraudel"
  description: "Hugo Giraudel is a CSS goblin, Sass hacker, margin psycho working at Edenspiekermann (Berlin) as a front-end developer."
  image: "http://app.resrc.it/s=w270,pd2/o=90/http://www.edenspiekermann.com/system/images/W1siZiIsIjIwMTUvMDQvMjcvMTIvMjQvNTIvMjMwL2h1Z29fZ2lyYXVkZWxfc3F1YXJlLmpwZWciXSxbInAiLCJ0aHVtYiIsIjUwMHg1MDAjIl1d/hugo_giraudel_square.jpeg"
```
