# JS backend on steroids

Guide to make and run the ultimate backend on NodeJS or V8.

![JS backend on steroids](src/js-backend-golden-path.png)

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
* Oh, and you want it all for free. Or (in worst case) nearly free. At least until you reach a significant userbase.

**ARE YOU NUTS?!**

No, you are not.<br>
This is the dream of the developer, and yes, it's all possible.

It's a developer guide with best practices as I personally see. First I wanted to make a small step-by-step guide just for myself, but then I realized it can be a nice tutorial with some extra comments.

## Two ways of deploying and the problem with migration

You have to choose.

* You can make a code that runs on a "traditional" servers. (Well we are talking about containers, so it's not truly traditional and definiately not unscalable, because you can scale that up as big as you want.)
* Or you can make a code that runs on the edge, without thinking about servers: deploy your code all over the world immediately.

There are cons and pros on both solution, but what should you choose at the beginning? Usually you don't know that yet, and this is the problem you are facing with.

### NodeJS and its problems

If you are going to build something new (and you have bigger plans than a home experiment), you could think it's better to choose the "traditional" way and start making servers, write codes that runs on NodeJS for example. Because this way you have the full control of the machine, and you can decide all of the dependencies.

But later when you get some visitors and decide to start to scale this up, you will get a bunch of new problems that you can not imagine in the beginning. Geo-routing, load balancers, auto scaling, certificates, CI/CD, secret management, and this list is far-far away from the end. 

You realize that it would be much better to run in the edge, and eliminate ALL of your scaling problems. But your software is in production, your code is not compatible, and you need to rewrite almost everything, making a bunch of new services and expanded infrastructure.

Nah. You know that you will stay with NodeJS.

And that's the right moment to start crying and hire DevOps, or stay up on long nights to start solving your expansion problems.

### Edge and its problems

On the other hand, you can choose edge computing at the beginning. Therefore you can just upload your simple backend code and immediately scale to the to whole globe magically from the first minute, faster than you can read this sentence.

But later you realize you need some more control over your servers. For example you need the `ghostscript` lib to do something with the user-uploaded PDFs. Of course you can not install `ghostscript` on edge servers, so what should you do now?

You realize that you should use a custom server with your full control of dependencies, but your code is made for the edge. Your main problem is not to only to rewrite everything, but building the whole scalable infrastructure, thinking about servers again in global scaling context, and the lack of your up-to-date DevOps knowledge. Yes, you can start crying now.

### The solution

So what is the right path for you application?

There are a lot of business decisions that influates the final choice, but at the beginning, you just simply don't know what will happen a year later.

**The best thing you can do is to make your project as compatible as possible with both of the two ways, therefore you can change your mind later. Just start on edge, because it's faster, easier and cheaper, then you will see the rest.**

(Cheap means free for small projects, and much cheaper on scaling projects.)

If you start on edge and later you realize that you need to do some heavy tasks, you can always make microservices, as small containers to do the job with the custom installed libs (like `ghostscript`). Also if you are fearing of some latter big trouble, like something you did not calculate with (for example extraoridany pricing), you can always switch back to NodeJS immediately.

**It's easy to go from edge to NodeJS.<br>
It's hard to go from NodeJS to edge.**

![JS backend on steroids](src/js-backend-golden-path-2.png)

That's why you want to make the most compatible codebase at it's heart, therefore you will need to modify just a few lines of code to go from one platform to another. This is the golden path.

Probably at the end you will arrive to the following mix:
- your API backends and small-task microservices will stay on edge at global scale (important to work fast and globally)
- your heavy-load tasks will go to containerized microservices (not improtant to work fast and globally)

...but anyways, you never want to lock-in. You want the golden path to move immediately if you need.

### CJS, ESM and compatibility issues

* CJS stands for CommonJS - think about the heart of NodeJS.
* ESM stands for ECMAScript Module - think about the heart of your browser, and the heart of edge (because edge uses V8 which implements ECMAScript).

When developing as CJS you normally import a CJS module like this:

```
const hogan = require('hogan.js')
```

When developing as ESM you normally import an ESM like this:
```
import { Hono } from 'hono'
```

Now read [this](https://adamcoster.com/blog/commonjs-and-esm-importexport-compatibility-examples). 

TLDR;
* You want to make your code as ESM, because edge works this way. And you can also run ESM in NodeJS. You don't need to change code.
* You don't want to make your code as CJS, because just NodeJS can run it, edge can't. Also you can't import every ESM module into CJS.

**So you will make ESM.**

Importing an ESM into your ESM:
```
import { Hono } from 'hono'
```
CJS module import looks like this in its documentation:
```
const hogan = require('hogan.js')
```
...but you can easily import CJS into your ESM:
```
import hogan from 'hogan.js'
```

Note that I did not use curly braces, therefore you will get the default component that CJS exports.

## Requirements

* Basic knowledge of software engineering, JavaScript and cloud concepts. We will not discuss terminology, but use technologies in this guide.
* `node` and `npm` installed in your machine - https://nodejs.org/
* Docker Desktop for running containers - https://www.docker.com/products/docker-desktop/

## What are we going to build?

A small backend webapp, with code structure, performance and security best practices. We will make a code that you can run on NodeJS and even on edge platforms, like Cloudflare Workers. This repository is just a bunch of snippets and notes about leverage the core concepts and deploy you software wordwide.

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

[ExpressJS](https://expressjs.com/) is the most famous NodeJS framework, however, we will use [Hono](https://hono.dev/). Hono is ultrafast, can do everything that ExpressJS does, and compatible all of the JavaScript runtimes (NodeJS, Cloudflare Workers, Lambda@Edge, Bun, Fastly and their friends). On the contrary, ExpressJS is only compatible with NodeJS.

It's true that Hono wasn't built for NodeJS originally, but we can make it work easily, therefore we can leverage this possibility to not rewrite our whole codebase if we decide to change our backend environment from NodeJS to the edge. We need an extra lib for NodeJS besides Hono, so in the `app` folder type:

```
pn i hono @hono/node-server
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

#### Secure headers, logging, pretty json

#### Template engine

#### Code structure

## Part2: Run in the cloud as NodeJS

### Dockerization

### Dockerhub build

### Digital Ocean deploy

### Cloudflare, protection, https

## Part3: Run in the cloud as an edge function