# espi-devblog
Edenspiekermann Developer Blog

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
