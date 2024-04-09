# JS backend on steroids

Guide to make and run the ultimate backend on NodeJS or V8.

![alt text](src/js-backend-on-steroids-diagram.png)

> [!CAUTION]
> It's an unfinished developer guide, containing typo and other mistakes. Come back later.

## Goals

* You want a server for your microproject, or your proof-of-concept app.
* You want to make it simple.
* You want a short learning curve to ship your first version fast.
* You want to test offline.
* You want to go online immediately...
* ...without a lot of configurations headache.
* It's a small project at start...
* ...but you want the possibility to scale up to serve the whole world...
* ...without thinking about rewriting anything in the code, and...
* ... without thinking about infrastructure, like load balancers, servers, encryption, etc.
* YOU. JUST. WANT. TO. CODE.
* NOW.
* Oh yes, and did I mention you want it all for free? At least until you reach a significant userbase.

ARE YOU NUTS?!

No, you are not.<br>
This is the dream of all developers, and it's all possible.<br>
Welcome to this tutorial.

## What is this?

It's a developer guide with best practices as I personally see. First I wanted to make a small step-by-step guide just for me, buit I realized I can do a nice tutorial if I add some more comments.

## Requirements

* Basic knowledge of software engineering, JavaScript and cloud concepts. We will not discuss terminology, but use technologies in this guide.
* `node` and `npm` installed in your machine - https://nodejs.org/
* Docker Desktop for running containers - https://www.docker.com/products/docker-desktop/

## What are we going to build?

A small backend webapp, with codeing, performance and security best practices. We will make a code that you can run on NodeJS and even on edge platforms, like Cloudflare Workers. This repository is just a bunch of snippets and notes about leverage the core concepts and deploy you software wordwide.

## Part0: get ready

Install some dependencies globally to you developer machine, if you did not do that already:
```
npm install -g nodemon pnpm
```

Nodemon is for auto-restarting you Node server, and pnpm is a "Fast, disk space efficient package manager". Make a shorthand for `pnpm` as `pn`: https://pnpm.io/installation#using-a-shorter-alias

## Part1: your software

### Git

Make a git repository of course.

### Init

In the empty root, make a folder called `app`. In terminal, go to this folder, then type ```pn init```. This will make you a standard starting point of your software.

### package.json

Modify to this:

```
...
"main": "app.js",
"type": "module",
"scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
},
...
```

* We made a new entrypoint: `app.js`.
* We use `node` for running in live, and `nodemon` for development purposes. Nodemon will restart the server automatically if you change a file.
* `"type": "module"` is required for use the module syntax. You could do otherwise, but it's recommended since we want to ship our app with not just NodeJS but later as an edge function with just minimum modifications. Without this setting you will get the 'Cannot use import statement outside a module' later. More info [here](https://www.dhiwise.com/post/fix-the-error-cannot-use-import-statement-outside-a-module) and [here](https://adamcoster.com/blog/commonjs-and-esm-importexport-compatibility-examples)

### Hono

[ExpressJS](https://expressjs.com/) is the most famous NodeJS framework. But we will not use that. I prefer [Hono](https://hono.dev/), because Hono is ultrafast, knows everything that ExpressJS knows, and compatible all of the JavaScript engines. ExpressJS is only compatible with NodeJS, but Hono has no other dependencies, so you can run Hono on NodeJS, Cloudflare Workers, Lambda@Edge, Bun, Fastly and so on.

In the `app` folder type:

```
pn install hono @hono/node-server
```

This will make the `package.json`, `pnpm-lock.yaml`, and the `node_modules` folder.

### .gitignore

Do not forget to make the .gitignore file in the root:

```
**/node_modules/*
```

### app.js

In the `app` folder add a file called `app.js`. Fill up with the following:

```
import { serve } from '@hono/node-server'
import { Hono } from 'hono'

const app = new Hono()
app.get('/', (c) => c.text('hello hono!'))

serve(app)
```

### First run

Open terminal, go to the `app` folder, then type
```
pn dev
```

Yay!

### Recommended upgrades

#### Helmet

## Part2: Dockerization

## Part3: Run in the cloud

## Part4: Run as edge function