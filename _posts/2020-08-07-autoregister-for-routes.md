---
layout: post
title:  "How auto register routers on Express app"
author: Jonathan Gómez
date:   2020-08-07 12:00:00 -0500
categories: dev
---

A little proyect that auto register routers on a directory to an Express app. By default I set the function poiting to ```_endpoints/``` dir
but have the option to point to another directory... but I dont test it hehe.

Anyway, lets jump to the code

First of all I had to install Node... I will jump this step because I think there are different ways a lot of tutorial of how to install it. But as a note,
I use nvm on zsh with oh my zsh on Linux.

Then I installed the dependencies:

```shell
npm init -y
npm i -D express 
npm i -D @babel/core @babel/node @babel/preset-env @babel/cli
```
I use babel to build and work with ES6 modules, and to build the project to deploy on Heroku. And this is the first time that I use Babel so don't expect
so much :P

> Using Babel is not obligatory but thats why you will see me using ES6 ```imports/exports``` and why I have to exclude some files from ```_endpoints/``` directory.

After that I create a directory named ```src/``` and another one inside this called ```_endpoints/``` like this:
```
node_modules/
src/
  _endpoints/
package.json
package-lock-json
```

Having that I create a file called ```src/app.js``` and wrote the next code:
```js
import express from "express";
import { registerEndpoints } from "./routing-registry";

const app = express();

export default app;
```
And another one called ```src/index.js``` with the code for starting the app:
```
import app from "./app.js";

const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`Listening to requests on http://localhost:${port}`);
});
```

At this point should be an error because the ES6 ```imports/exports``` and I had two options:
* Add ```"type": "module"``` property on my ```package.json```.
* Add Babel (or other tool like these).
I chose the second one because I have so much curiosity on learn how to work with these tools.
