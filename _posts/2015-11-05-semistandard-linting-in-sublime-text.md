---
layout: post
author: Hugo Giraudel
title: "Semistandard linting in Sublime Text"
---

# Semistandard linting in Sublime Text

At Edenspiekermann, we recently decided to drop custom [eslint](http://eslint.org/) configuration file and to use [semistandard](https://github.com/Flet/semistandard) instead (we could have used [standard](https://github.com/feross/standard) but we do like semi-colons). This move aims at normalizing our linter configuration across projects in order to facilitate teams’ turn-over.

One challenge though has been to enable semistandard linting in Sublime Text. After a few unfortunate failures and a dozen of Google searches, we finally succeeded in making it work. Here is how.

1. Install [SublimeLinter](http://www.sublimelinter.com/en/latest/) package. 
2. Install [SublimeLinter-contrib-semistandard](https://github.com/Flet/SublimeLinter-contrib-semistandard) package. This has to be done after having installed SublimeLinter, as it is the core dependency.

At first, we thought this and restarting Sublime Text would be enough, but it was not. Sublime Text’s console (yes, it exists) threw an error like:

```
SublimeLinter: env: node: No such file or directory
```

We use [nvm](https://github.com/creationix/nvm) to handle [npm](https://www.npmjs.com/) versions so there is no global install of npm. Because of this, SublimeLinter could not run semistandard. The solution was simple, we only had to make nvm use a default version:


```
nvm alias default stable
```

That’s enough for SublimeLinter to run semistandard! Also, the best thing is that it uses the local semistandard version (installed in `node_modules` folder as a dependency), which means no version discrepancy between developers!
