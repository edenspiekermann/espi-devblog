---
title: "Beyond Living Styleguides"
author: eric-schaefer
layout: post
---

#### Or, why a component library can be more useful.

## Background

Living style guides were conceptualized to document user interface elements as they evolve, using production (live) CSS. They have been an essential way to create a project’s source of truth for developers and designers. The Style Guide sits alone, on its throne, separate from the hot mess of a code-base it needs to influence.

👑

And that’s a problem…

The living style guide is still a partially separate code-base that needs love and maintenance. And it probably exists to show your team and designers how nicely your UI elements _could_ look. Of course that style guide `<button>` element only has two small words in it. Because if it had more, then, well…

![CSS](http://i.imgur.com/v9bw6xj.png)

## Edge cases

I can guess with 95% certainty that your style guide was not built to show or handle the edge cases. If your UI is multi-lingual, or rendered as a function of data coming from an API somewhere, then this problem will seem familiar. Just wait for [Hubert Blaine Wolfeschlegelsteinhausenbergerdorff, Sr.](https://en.wikipedia.org/wiki/Hubert_Blaine_Wolfeschlegelsteinhausenbergerdorff,_Sr.) to sign up, then watch your world burn. Reality is hard.

Moreover, it’s entirely probable that there are elements in your style guide that are no longer even in your production code. Orphaned elements tossed-out in some design overhaul long ago. Or even worse, the other way around, where a code hotfix for some UI problem never bubbled-up into the style guide.

Before you reach for your pitchforks, I want to emphasize that living style guide concepts have created a strong baseline for how we develop new projects at Edenspiekermann. We have used a modified version of [Tdcss.js](http://jakobloekke.github.io/tdcss.js/) with great success.

There’s gotta be a better way though. One that is bound to components in production, and accurately reflects live data supplied to those components.

Enter…

## Component libraries

Many of our projects are already built with a component-based approach. This is a wonderful side-effect of using [React](https://facebook.github.io/react/). All UI elements in a well-built React application end up being self-encapsulated (markup and styles bundled together) and composable. This simple fact has far-reaching implications:

* We can import all our components and render each one into a library.
* We can minimize the chances of dead code and designs.
* We can eliminate duplicated effort to keep production elements in sync with the styleguide.
* We can auto-document how often and where a component is used.

This all sounds great, yeah?

If you are using React, there are some component library toolkits that can hook into your existing project. We found it useful to break down just how many have appeared in the last couple years:

*PAUL’S ILLUSTRATION GOES HERE*

### [Cosmos](https://github.com/skidding/cosmos)

> “Cosmos is a JavaScript […] tool for designing truly encapsulated React components.”

![Cosmos](https://cloud.githubusercontent.com/assets/250750/8532005/e6d3b3bc-2433-11e5-9fc3-39a9288198e9.gif)

### [Devcards for ClojureScript](https://github.com/bhauman/devcards)

Ok this isn’t _really_ React-specific, but could be used with [Om](https://github.com/omcljs/om) or [Reagent](https://holmsand.github.io/reagent/). Devcards author Bruce Hauman is [really fun to listen to](https://www.youtube.com/watch?v=G7Z_g2fnEDg) though. He describes it as more of a “literate interactive coding” tool. Perfect for collaboration with a designer. You just end up with a comprehensive overview of your components in all their crazy states.

![Devcards](https://camo.githubusercontent.com/064d7c9d774ef7cfce36e695f9d800becd05fd00/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f626861756d616e2d626c6f672d696d616765732f64657663617264732d616374696f6e2d73686f742e706e67)


### [React Styleguide Generator](https://github.com/pocotan001/react-styleguide-generator)

> Easily generate a good-looking styleguide by adding some documentation to your React project.

![React Styleguide Generator](https://cloud.githubusercontent.com/assets/869065/8392279/7f3811ae-1d20-11e5-9707-864d5994ba49.png)

### [UI Harness](https://github.com/philcockfield/ui-harness)

> Create, isolate and test modular UI components in React

This one falls more into the “developer tool” category, but is interesting nonetheless.

![UI Harness](https://cloud.githubusercontent.com/assets/185555/12281176/be80e442-b9f8-11e5-9991-6b678bbc067f.png)

### [React Styleguidist](https://github.com/sapegin/react-styleguidist)

> React Styleguidist is a style guide generator for React components. It lists component propTypes and shows live, editable usage examples based on Markdown files.

![Styleguidist](https://camo.githubusercontent.com/c83d537044dbe50972ab4c3c1351529674c92926/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f662e636c2e6c792f6974656d732f336930453144314c3163316d31733247316430792f53637265656e2532305265636f7264696e67253230323031352d30392d3234253230617425323030392e3439253230414d2e676966)

### [React BlueKit](https://github.com/blueberryapps/react-bluekit)

> BlueKit automatically generates a library from your React components with editable props and live preview.

This one seems like an underdog in terms of Github stars, but looks really polished.

![BlueKit](https://cloud.githubusercontent.com/assets/547148/17784131/2c448104-6530-11e6-8efb-a01c2071cef7.png)


### [Atellier](http://scup.github.io/atellier)

> The smartest way to share interactive components with your team

A very slick component library, although the last release was nearly five months ago.

![Atellier](http://scup.github.io/atellier/images/browser-window.png)


### [Carte Blanche](https://github.com/carteb/carte-blanche)

> Carte Blanche is an isolated development space with integrated fuzz testing for your components. See them individually, explore them in different states and quickly and confidently develop them.

A great tool unveiled at the ReactEurope conference. Max and Nik put a lot of thought into this, and [their talk is worth watching](https://www.youtube.com/watch?v=0IkWuXeKPV0&feature=youtu.be&list=PLCC436JpVnK0LTDKW3O_BGTZnrZ8dBAof).

![Carte Blanche](https://cloud.githubusercontent.com/assets/7525670/15761445/8ae05d4a-2918-11e6-8573-bd9bd0ef2330.png)


### [React Storybook](https://github.com/kadirahq/react-storybook)

> With React Storybook, you can develop and design UI components outside your app in an isolated environment.

This is by far the most widely used component library for React. Part of its success could probably be attributed to [Arunoda Susiripala’s excellent article](https://voice.kadira.io/introducing-react-storybook-ec27f28de1e2#.5l6xh5z7f) that introduces the problems that React Storybook tries to solve.

![React Storybook](https://d262ilb51hltx0.cloudfront.net/max/1200/1*VFYNsj1ZVmVXd5M9J8wIUg.gif)


## Wrap-up

Component library tools that can be hooked into an existing code-base seem like the future of living style guides. The emergence of componentized applications has made possible the chance better isolate elements of UIs. There is real value in having a quick environment in which to test components, and it improves collaboration between designers and developers. But most of all, we all save time.
