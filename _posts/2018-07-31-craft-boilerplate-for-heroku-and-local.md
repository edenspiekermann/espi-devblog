---
title: 'Craft 3 Boilerplate: Use it locally and on Heroku'
author: dimitri-steinel
layout: post
mainImage: ""
mainImageAlt: ""
--- 

### First contentful paint
To make sure we can seamlessly work in teams, we have a specific requirements for our hosting environment.
Currently, our hosting solution of choice is [Netlify](https://www.netlify.com/) and [Heroku](https://www.heroku.com/). 
Netlify is mostly for our static websites. Heroku is for almost all other projects.
With [Craft 3](https://craftcms.com/news/craft-3), which was released in April’18, it is already much easier to install the CMS as [composer package](https://packagist.org/packages/craftcms/cms) and host it. But there were still some things we need to take care of in order to host it on Heroku.

Here is a guide on how to set up a Craft 3 project on Heroku.

### Don’t want to read or the quick and dirty start
This button has been made for those who just want to have a fresh Craft 3 instance on Heroku. Click the button and directly deploy our public [boilerplate repository](https://github.com/edenspiekermann/craft-heroku-boilerplate) to a new instance on your Heroku account. 

Go ahead and try:
[![Deploy](https://www.herokucdn.com/deploy/button.svg){:class="img--little-margin"}](https://heroku.com/deploy?template=https://github.com/dsteinel/craft-heroku-test-project/tree/master)

After the install process, visit the URL of your app and add `/admin/install` to it. For example: 
`https://HERE-GOES-YOUR-URL.herokuapp.com/admin/install`

Fill out all the details from the guide and you are good to go!

## The slower but I know what I am doing start
### Set up a new project
First we need to clone our Boilerplate Repo into a local folder:
{% highlight bash %}
  $ git clone git@github.com:edenspiekermann/craft-heroku-boilerplate.git
{% endhighlight %}

Rename the folder and make it your project:
{% highlight bash %} 
  $ mv craft-heroku-boilerplate NEW-PROJECT-NAME
{% endhighlight %}

Remove the Github connection and connect the folder with your own Git repository. Go and create a new and empty Git repository. There must not be any `.git` or `README.md` file. After it is created, go with:
{% highlight bash %}
  $ cd craft-heroku-boilerplate
  $ rm -rf .git
  $ git init
  $ git add .
  $ git commit -m '🎈 project start 🎈'
  $ git remote add origin YOUR-REMOTE-PROJECT-URL
  $ git push --set-upstream origin master
{% endhighlight %}

### Use it
#### a) Locally
* Create a `.env` file in the root of your project
* Copy over all dummy content from `.env.example`. These are all the details Craft needs for installation.
* Install all dependencies 
{% highlight bash %}$ composer install{% endhighlight %}
* In Mamp, make `web` the root path you project
![MAMP](https://res.cloudinary.com/dsteinel/image/upload/v1532511859/Screen_Shot_2018-07-25_at_11.43.20.png){:class="img--little-margin"}
* Go to `localhost:8888/admin/install`
* Follow the Craft install guide
* With your right hand, make a fist (not a tight one) 
* Smoothly extend both your pinky and your thumb
* Lightly shake your hand (too fast makes you like a tourist, and too slow make you look stupid)
* Softly but confident vocalise the word "Shaka"
* Now let's start coding 🎈

#### b) Optional: Deploy to Heroku
This step is optional. If you want to deploy the project to Heroku then read on. If you only want to work locally, skip the section and jump to [Start working with the project](#start-working-with-the-project).
To make your repository work on Heroku, we first have to update the reference URL with your freshly created repository.
Heroku reads all the deploy details from the [app.json](https://devcenter.heroku.com/articles/app-json-schema) file. So we need to go there and replace the `"repository"` URL with your repository URL. 

Push the changed `app.json` to your GitHub repository and adjust the following link with your repository and branch:
`https://heroku.com/deploy?template=https://github.com/YOU_REPOSITORY/tree/YOUR_BRANCH_NAME`

For example: `https://heroku.com/deploy?template=https://github.com/dsteinel/craft-heroku-test-project/tree/master`

After you adjusted the link, visit it in your browser.
You will see a Heroku page for creating a new instance.
![Heroku create new app screen](https://res.cloudinary.com/dsteinel/image/upload/v1532511156/Screen_Shot_2018-07-25_at_11.32.07.png){:class="img--little-margin"}

It will also install a free plan of a MariaDB and connect it with Craft. 
Please fill out all the details and don't forget to choose the region. If you are in Europe, please use Europe and US if you are in the US or nearby. If you choose the wrong region, it could be that your website is slower than it could be.

If you have multiple Users, it is recommended, that you upgrade at least the database to a paid plan. It can happen, that the `max_connections` of the free database plan is used up, when you have too much database traffic. This means, that you can not access the website anymore (Error 500) and you have to upgrade the database plan.

Depending on the traffic, you also may have to upgrade the Heroku servers to a paid plan.

##### Heroku Post install
After the deployment process is successful, go to your new created website and add `/admin/install` to the URL. Craft will help you to make the setup complete.
![Craft CMS install screen](https://res.cloudinary.com/dsteinel/image/upload/v1532511530/Screen_Shot_2018-07-25_at_11.36.45.png)

### Start working with the project
* Use the recommended node version with `nvm use`
* Install all dependencies with `yarn install`
* Start MAMP (make `web` the root path you project)
* Run `yarn watch` from the Terminal. This will start a web-server and proxy port :8888 (MAMP) to :3000 (webpack)
* Go to `localhost:3000` and enjoy hot reloading
* If you want to make the bundle production ready, do `yarn build`