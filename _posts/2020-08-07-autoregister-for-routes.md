---
layout: post
title:  "Auto register routers on Express app"
author: Jonathan Gómez
date:   2020-08-07 12:00:00 -0500
categories: dev
---

__The general idea of this post is to present the idea that you just have to create the file with the endpoint, export it in the same file, and it can be register automatically to the app.__

### Note
> __This is just to show my approach and to receive feedback about what I just had done__ 😬

I'm new with the use of frameworks (and writing on english… and writing here on dev) and coding in general but the times that I use Express I didn’t like his openness and all the work that is around to configure everything. __I’m still new so maybe there are some ways to do it in an easy way. But I don’t know that’s why I’m open to seeing others ideas just for fun.__

> 

Also this post it’s to remember the process and to start posting more stuff. So if you just want to know about the auto register it's on the Resumé part. But anyway, let's jump to the code.

## Main

Here is the main stuff for what I want to show you.

[>> But you can find the rest of the project configuration here <<](#extra)

### Endpoint

I created the first endpoint on a file called `main.endpoint.js` with the basic configuration for and endpoint:
```js
// src/_endpoints/main.endpoint.js
import express from "express";
const router = express.Router();

const path = "/";

router.get("", (req, res) => {
  res.json(
    {
      hello: "Hello",
      world: "World",
    }
  );
});

export { path, router };
```

### Autoregister

I created a file called `src/routing-register.js` and here is where the magic happens (at least to me that I feel so happy to see when it worked):
```js
// src/routing-register.js
import path from "path";
import fs from "fs";

export const autoregisterEndpoints = (app, pathEndpoints = "_endpoints") => {
  const endpointsPath = path.join(__dirname, pathEndpoints);
  fs.readdirSync(endpointsPath).forEach((file) => {
    let include = includeFile(file);

    if(include){
      let { path, router } = require(`./${pathEndpoints}/` + file);
      app.use(path, router);
    }
  })
}

const includeFile = (file) => {
  const file_splited = file.split('.');
  let extension = file_splited[file_splited.length - 1]
  return extension == "js"
}
```
> *I exclude files that do not end with `.js` because the builded project contains files with `.map` extension for every `.js` file.*

### Execution

I had to execute the function on the `src/app.js` file passing the main app as the parameter:
```js
// src/app.js
import express from "express";
import { registerEndpoints } from "./routing-register";

const app = express();

autoregisterEndpoints(app); // << here

export default app;
```

### Ready 🎉

And it was done! The rest is just the configuration with Babel and is just a plus ultra (I hope you get it).

## Extra

(Project Configuration)

First of all I had to install Node... I will skip this step because I think there are different ways to install it. But as a note, I use nvm on zsh with oh my zsh on Linux.

### 1.- Dependencies

I installed the dependencies:

```shell
mkdir autoregister && cd autoregister
npm init -y
npm i -D express 
npm i -D @babel/core @babel/node @babel/preset-env @babel/cli
```
- The first command is to create a directory called `autoregister/` and move into it.

- The second command is to initialize and Node project in the current directory and it will have the default configuration.

- The third and four command install the dependencies:
  - express
  - @babel/core
  - @babel/node
  - @babel/preset-dev
  - @babel/cli

I used babel to work with ES6 modules and to build the project to deploy on Heroku.

This is the first time that I use Babel so don't expect so much hehe and I use it here just as an excuse to try it on something.

> _Using Babel I have to exclude some files from the `_endpoints/` directory on the builded project._

### 2.- Project Structure

After that I created a directory named `src/` and another one inside this called `src/_endpoints/`:
```
node_modules/
src/
  _endpoints/
package.json
package-lock-json
```

### 3.- Code Structure

Having that I created a file called `src/app.js` and write the next code:
```js
// src/app.js
import express from "express";

const app = express();

export default app;
```
And another one called `src/index.js` with the code to start the app:
```js
// src/index.js
import app from "./app.js";

const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`Listening to requests on http://localhost:${port}`);
});
```

### 4.- Scripts for build & run

I had to add the scripts needed to build and start the project on the `package.json` inside the `"scripts"` property:
```json
{
  "scripts": {
    "start": "npm run build && node ./build/index.js",
    "build": "npm run clean && npm run build-babel",
    "build-babel": "babel -d ./build ./src -s",
    "clean": "rm -rf build && mkdir build"
  }
}
```

### 5.- ES6 Modules support

At this point, the code will not only run but compile and try to run the compiled version of the project that will be inside of an auto generated directory called `build/`.

But still not worked because the ES6 `imports/exports` and I had two options:
* Add `"type": "module"` property on my `package.json`.
* Add Babel (or other tool like these).

> I chose the second one because __I had so much curiosity__ on how to work with these tools.

To configure Babel to use ES6 modules I had two options again:
* Create a file called `.babelrc` with the next code:

```json
{
  "presets": [
    "@babel/preset-env"
  ]
}
```
* Add the next property to the bottom of my `package.json`:

```json
{
  "babel": {
    "presets": ["@babel/preset-env"]
  }
}
```
> I chose the second one but I think it's the same for both cases.

__🎉 After doing this the project worked. Now I had to add the endpoints and the autoregister 🎉__ 


If you follow these steps and run the app I hope that you will be able to see this on http://localhost:3000/:
```json
{
  "hello": "Hello",
  "world": "World"
}
```

Now if I want to add a new endpoint I just have to create a new file on the `src/_endpoint/` like the `main.endpoint.js` file and modify the `path` constant.
