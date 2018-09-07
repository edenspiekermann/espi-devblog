---
title: 'Craft 3 - Heroku Boilerplate'
author: dimitri-steinel
layout: post
mainImage: "http://res.cloudinary.com/dsteinel/image/upload/c_scale,w_1012/v1536323225/Espi/blog/heroku-craft/craft-heroku-quickstart.jpg"
mainImageAlt: "Yes this is a rocket"
--- 

To make sure we can seamlessly work in teams, we have a specific requirements for our hosting environment.
Currently, our hosting solution of choice is [Netlify](https://www.netlify.com/) and [Heroku](https://www.heroku.com/). 
Netlify is mostly for our static websites. Heroku is for almost all other projects.
With [Craft 3](https://craftcms.com/news/craft-3), which was released in April’18, it's already much easier to install the CMS as a [composer package](https://packagist.org/packages/craftcms/cms) and host it. But there were still some things we needed to take care of in order to host it on Heroku.

This is a guide on how to set up a Craft 3 project on Heroku.

### The quick and dirty start
This button has been made for those who just want to have a fresh Craft 3 instance on Heroku. Click the button and directly deploy our public [boilerplate repository](https://github.com/edenspiekermann/craft3-heroku-starterkit) to a new instance on your Heroku account. 

Go ahead and try:
[![Deploy](https://www.herokucdn.com/deploy/button.svg){:class="img--little-margin"}](https://heroku.com/deploy?template=https://github.com/edenspiekermann/craft3-heroku-starterkit/tree/master){:target="_blank"}

After the install process, visit the URL of your app and add `/admin/install` to it. For example: 
`https://YOUR-URL-GOES-HERE.herokuapp.com/admin/install`

Fill out all the details from the guide and you are good to go!
![Heroku create new app screen](https://res.cloudinary.com/dsteinel/image/upload/v1533126210/Espi/blog/heroku-craft/2018-08-01_14.21.56.gif){:class="img--little-margin"}


## Start from scratch
### Set up a new project
First we need to clone our Boilerplate Repo into a local folder:
{% highlight bash %}
  $ git clone git@github.com:edenspiekermann/craft-heroku-boilerplate.git
{% endhighlight %}

Rename the folder and make it your project:
{% highlight bash %}
  $ mv craft-heroku-boilerplate NEW-PROJECT-NAME
{% endhighlight %}

Now we need to remove the Github connection and connect the folder with your own Git repository. 
{% highlight bash %}
  $ cd craft-heroku-boilerplate
  $ rm -rf .git
  $ git init
  $ git add .
  $ git commit -m '🎈 project start 🎈'
{% endhighlight %}

Go and create a new and empty Git repository with your account. There must not be any `.git` or `README.md` file. After it is created, go with:
{% highlight bash %}
  $ git remote add origin YOUR-REMOTE-PROJECT-URL
  $ git push --set-upstream origin master
{% endhighlight %}

### Use it
#### Deploy to Heroku
To make your repository work on Heroku, we first have to update the reference URL with your freshly created repository.
Heroku reads all the deploy details from the [app.json](https://devcenter.heroku.com/articles/app-json-schema) file. So we need to  replace the `"repository"` URL with your repository `YOUR-REMOTE-PROJECT-URL`. 

Push the changed `app.json` to your GitHub repository and adjust the following link with your repository and branch:
`https://heroku.com/deploy?template=https://github.com/YOUR-REMOTE-PROJECT-URL/tree/YOUR-BRANCH-NAME`

For example: `https://heroku.com/deploy?template=https://github.com/edenspiekermann/craft3-heroku-starterkit/tree/master`

After you adjusted the link, visit it in your browser.
You will see a Heroku page for creating a new instance.
![Heroku create new app screen](https://res.cloudinary.com/dsteinel/image/upload/v1532511156/Screen_Shot_2018-07-25_at_11.32.07.png){:class="img--little-margin"}

It will also install a free plan of a MariaDB and connect it with Craft. 
Please fill out all the details and don't forget to choose the region. If you are in Europe, please use Europe and US if you are in the US or nearby. If you choose the wrong region, it could be that your website is slower than it could be.

If you have multiple Users, it is recommended, that you upgrade at least the database to a paid plan. It can happen, that the `max_connections` of the free database plan is used up, when you have too much database traffic. This means, that you can not access the website anymore (Error 500) and you have to upgrade the database plan.

Depending on the traffic, you also may have to upgrade the Heroku servers to a paid plan.

#### Heroku Post install
After the deployment process is successful, go to your new created website and add `/admin/install` to the URL. Craft will help you to make the setup complete.
![Craft CMS install screen](https://res.cloudinary.com/dsteinel/image/upload/v1532511530/Screen_Shot_2018-07-25_at_11.36.45.png){:class="img--little-margin"}

#### Use it locally
To complete your development environment, you should make the project running on your local machine as well:
* Create a `.env` file in the root of your project
* Copy over all dummy content from `.env.example`. These are all the details Craft needs for installation.
* Install all dependencies 
{% highlight bash %}$ composer install{% endhighlight %}
* In Mamp, make `web` the root path you project
![MAMP](https://res.cloudinary.com/dsteinel/image/upload/v1532511859/Screen_Shot_2018-07-25_at_11.43.20.png){:class="img--little-margin"}
* Go to `localhost:8888/admin/install`
* Follow the Craft install guide

##### Having troubles with PHP extensions?
If you have any issues with missing php extensions during `composer install` command, you have to make sure the extension `ext-mbstring` and `ext-imagick` are installed. For OSX, it is best to install `imagemagick` and `php` (must be >7) with [homebrew](https://brew.sh/index_de) and `imagick` with pecl (PHP extension manager):
{% highlight bash %}
  $ brew install imagemagick
  $ brew install pkg-config
  $ pecl install imagick
{% endhighlight %}


### Start working with the project
* Use the recommended node version with `nvm use`
* Install all dependencies with `yarn install`
* Start MAMP (make `web` the root path you project)
* Run `yarn watch` from the Terminal. This will start a web-server and proxy port :8888 (MAMP) to :3000 (webpack)
* Go to `localhost:3000` and enjoy hot reloading
* If you want to make the bundle production ready, do `yarn build`

## Plugins which come with the project
### Cloudinary
[Craft3 Cloudinary](https://github.com/timkelty/craft3-cloudinary) is an integration of the cloud-based image management [cloudinary](https://cloudinary.com/) to your Craft3 project. 

### Set up Cloudinary in Craft
1. Go to your `Admin -> Settings -> Assets`
2. Create a new Volume
3. Type in a name you like (f.e. `Cloud Images`)
4. Enable `Assets in this volume have public URLs`
5. You can decide on your Base URL (f.e. `@web` is the root of your website but you can also go with `@web/images` if you want to have `http://yourwebsite.com/images/` as your public image path)
6. Volume Type must be `Cloudinary`
7. Now fill in the Cloudinary credentials
8. Go to the Assets and upload the first image

### Blitz
The [Blitz Plugin](https://github.com/putyourlightson/craft-blitz) is only for production. It is a intelligent static file caching for creating lightning-fast sites. If you enable this, your site will be very fast, but you will not see the debugger toolbar. So make sure to disable it in development from the admin panel.

### Redactor
Most projects do need a wysiwyg editor. [Redactor](https://imperavi.com/redactor/) claims to be the most advanced and human-friendly one. Well ... it is a very nice and customisable editor.
[GitHub](https://github.com/craftcms/redactor)

### Gatekeeper
If the page is still under construction, it is a good idea to password protect it. Therefore we use [Gatekeeper](https://github.com/tomdiggle/craft-gatekeeper).


### Important notice

#### Database
If you want to develop locally, I can highly recommend you to use a local database, otherwise the website will be quite slow.
To change the database, head over to your `.env` variable, change the value of `JAWSDB_MARIA_URL` and restart the server.

#### Procfile
Craft has its main folder not in the root directory (in this project it is the `web` folder), so we need to do some adjustments on the Heroku web server settings. Heroku provides optional configurations in the [Procfile](https://devcenter.heroku.com/articles/deploying-php#the-procfile). 
In this project we need to start an Nginx as a web server and make `web` the root directory of the server.

I would recommend to not change the name of the `web` folder. 
If you feel like changing it, please read the [Docs](https://docs.craftcms.com/v3/directory-structure.html#web) and don't forget to change the folder path after the boot-script in the `Procfile`.

#### Database
This project is using MariaDB instead of MySQL. MariaDB is a complete drop-in-replacement for MySQL. If you need to migrate some datas, you should not have problems here. If you do, you can change the database connection at any time in the Heroku interface by replacing the `JAWSDB_MARIA_URL` URL with your database URL (`settings --> config vars`).
