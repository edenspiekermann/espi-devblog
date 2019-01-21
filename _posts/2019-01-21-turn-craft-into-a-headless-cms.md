---
title: 'Turn Craft 3 into a headless CMS'
author: dimitri-steinel
layout: post
mainImage: 'https://res.cloudinary.com/dsteinel/image/upload/v1548088797/Espi/blog/transform-craftcms-into-a-headless-cms.png'
mainImageAlt: 'Yes this is a rocket'
---

### What is a headless CMS?

The classic CMS approach is to couple the presentational layer with the CMS directly. With this monolithic approach of building a CMS, your developers need to use a specific template language, and you lose much flexibility. This kind of CMS outputs content for only one channel: web browsers.
<br />
You may think that this is enough for now, but we will prove that you can do so much more with your content than show it on one channel. Instead, we want to enable an omnichannel experience.

### Why would I want to have a headless CMS?

**Performance, Security and Scalability.**
<br />
In the headless approach there is no presentational layer out-of-the-box, and therefore developers are not bound to any templating or programming language to create one. With a headless CMS instead, you can have multiple heads, and this gives us a lot of freedom, scalability, and flexibility. Think of an app, voice interface, AR or any other new technology you need to deliver content to.
<br />
<br />
A Static Site Generator like [GatsbyJS](https://www.gatsbyjs.org/) or [Nuxt.js](https://nuxtjs.org/) (there are [many others](https://www.staticgen.com/)) fetches all the content from your CMS via an API and generates old fashioned static sites. Static does not mean the site cannot be interactive. It just means that the site doesn't need to communicate with the CMS to fetch the content. If you are looking to improve the security of your site, a static site can live on another server without any connection to the CMS. The connection happens during build time which means, our frontend is blazing fast!

### Headless Craft

Let's see how to make use of Craft as a headless CMS. We'll use CraftQL to access content in Craft, and Gatsby to render it into static HTML. Here we go.

### CraftQL

Our static site generator of choice is GatsbyJS. With the plugin [CraftQL](https://github.com/markhuot/craftql) we can connect our GatsbyJS with the CMS over GraphQL.

Let's start with installing the plugin:
<br />
`composer require markhuot/craftql:^1.0.0`
<br />
<br />
Once installed, we need to create an endpoint. Go to your CraftCMS admin and head over to **settings —> CraftQL**.
Enter the URI for your endpoint or stíck with the default 'api'. Think of a name and generate a new token.
Make sure you open the settings of your token you just created. Here you can adjust the power of the token. Should this token be read-only or can you also mutate entries with your token?

Remeber, your **api endpoint** will be your CraftCMS URL + URI name (default: api): https://your-craftcms-url.com/api

Do not forget to update the API token settings if you add a new section. Otherwise the API does not know that there is a new section.

![Screenshot of CraftQL settings](https://res.cloudinary.com/dsteinel/image/upload/c_scale,w_1500/v1546602480/Espi/blog/craft%20to%20headless/espi-blog-craft-to-headless-craftql-settings.png)

### Gatsby

Let’s jump in the frontend and prepare the project with Gatsby.

- Install the Gatsby command line tool: `npm install --global gatsby-cli`.
- Create a new Gatsby project: `gatsby new gatsby-site`
- Go into the project folder (_gatsby-site_ in my case) and install the [GraphQL plugin for Gatsby](https://www.gatsbyjs.org/packages/gatsby-source-graphql/): `npm install --save gatsby-source-graphql`

### Setup the GraphQL plugin

Create a **.env** file in your project root. The file should be now next to your package.json. We want to hide all credentials with this .env file, so if you are using any version control, make sure to .gitignore (YMMV) the .env file. We do not want to have the credentials visible in a repository.
<br />
Edit the **.env** file and add:
<br />
{% highlight bash %}
GRAPHQL_TOKEN=“ENTER HERE THE TOKEN FROM CRAFTCMS“
API_URL=“ENTER HERE THE URI WE SET IN CRAFTCMS“
{% endhighlight %}

Go to the file Gatsby configuration file (**gatsby-config.js**) and add the graphql plugin to the list:

{% highlight js %}
{
resolve: `gatsby-source-graphql`,
options: {
url: `${apiUrl}`,
typeName: "Craft",
fieldName: "craft",
headers: {
Authorization: `bearer ${graphqlToken}`,
},
start_url: '/',
background_color: '#663399',
theme_color: '#663399',
display: 'minimal-ui',
}
}
{% endhighlight %}

You also need to fetch the credentials we set up in the **.env** file. To do that, put the following lines at the very top of your **gatsby-config.js** file.

{% highlight bash %}
require('dotenv').config();
const graphqlToken = process.env.GRAPHQL_TOKEN;
const apiUrl = process.env.API_URL;
{% endhighlight %}

The complete **gatsby-config.js** should now look like this:

{% highlight js %}
require('dotenv').config()

const graphqlToken = process.env.GRAPHQL_TOKEN
const apiUrl = process.env.API_URL

module.exports = {
siteMetadata: {
title: `Edenspiekermann CraftQL test project`,
description: `Let's make CraftCMS headless`,
author: `@edenspiekermann`,
},
plugins: [
`gatsby-transformer-sharp`,
`gatsby-plugin-sharp`,
{
resolve: `gatsby-source-graphql`,
options: {
url: `${apiUrl}`,
typeName: 'Craft',
fieldName: 'craft',
headers: {
Authorization: `bearer ${graphqlToken}`,
},
start_url: '/',
background_color: '#663399',
theme_color: '#663399',
display: 'minimal-ui',
},
},
],
}
{% endhighlight %}

### Fetch the content

For the last step, we need to fetch the content from our API. Go into your Craft Admin panel and make sure you have a page with the handle **homepage**.
<br />
Rename the file **pages/index.js** into **pages/index.jsx** and add the following content:

{% highlight bash %}
import React from 'react'
import { StaticQuery, graphql } from 'gatsby';

import Layout from '../components/layout';

const IndexPage = () => (
<StaticQuery
query={graphql`{ craft { home: entries(section: [home]) { ... on Craft_Home { id title slug } } } }`}
render={({ craft }) => {
const homepage = craft.home[0];
const {
id,
title,
slug,
} = homepage;

      return (
        <Layout>
          <h1>{title}</h1>
          <p>Entry Slug: {slug}</p>
          <p>Entry Id: {id}</p>
        </Layout>
      );
    }}

/>
);

export default IndexPage;
{% endhighlight %}

Now everything should be set. Start the gatsby server with **gatsby develop**, start — if needed — your MAMP or similar web development solution and go to **localhost:8000**. You should see the title, slug, and id of your home entry.
![Gatsby testsite](https://res.cloudinary.com/dsteinel/image/upload/c_scale,w_1500/v1546603973/Espi/blog/craft%20to%20headless/espi-blog-craft-to-headless-testsite.png)
To keep this tutorial simple, we fetched only the CraftCMS default fields. Not it's up to you to extend your data model. You can query any field you want via the API we showed here. Use the GraphQL interface for a more visual approach to building queries: **http://localhost:8000/___graphql**. Gatsby has some nice caching going on with which the live-reload is nice and smooth. Make sure if you change anything in the database you need to delete the **.cache** folder and start the Gatsby task again. Otherwise, you can not see any updates.

<br />
Head over to the [Official GatsbyJS Quick Start](https://www.gatsbyjs.org/docs/quick-start) if you need some help here.

### Wrap up

CraftCMS is easy enough to use as a headless CMS. Go on and explore, and enjoy the performance, security, and scalability of being headless.
