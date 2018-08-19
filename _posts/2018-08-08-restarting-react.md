---
layout: post
title:  "Restarting React Course: Part One"
date:   2018-08-08 18:56 +0100
author: "Sean"
categories: 
---

So a while ago I was working through Andrew Mead's [The Complete React Web Developer Course (with Redux)](https://www.udemy.com/react-2nd-edition/) on Udemy. I didn't follow it exactly; I opted to install the latest version of each module instead of the version specified in the course. Coming back to the course, I've found there are a bunch of different issues with the current project.

I diverged from the course quite a lot using the `webpack-merge` module to create separate configurations for development and production. I think I'm going to have to go through it bit by bit to figure it out.
Using `yarn dev-server` to try and launch the development version of the project gave the following error:

```
/Users/seanli/courses/react-course-expensify-app/webpack.common.js:41
    new webpack.DefinePlugin({
        ^

ReferenceError: webpack is not defined
```

It turns out that webpack wasn't required in the `webpack.common.js` file that is used in both configurations. Adding

```javascript
const webpack = require('webpack');
```

to the file managed to fix that problem. After this error stopped happening, the following occured:

```
Error: 'sass-loader' requires 'node-sass' >=4
```

Seems like the problem is node-sass, the version listed in package.json was 4.9.1. Looking online, it seems like this may be a problem with having node-sass cached. Uninstalling with `yarn remove node-sass` and installing again with `yarn add node-sass` solved the problem.
This problem may have been caused by the fact that I've been using nvm to manage node versions.
When I originally installed the modules, it may have been using the wrong node version and I have since switched to node version 8.

The next error was:

```
[2018-08-11T09:59:11.221Z]  @firebase/database: FIREBASE FATAL ERROR: Can't determine Firebase Database URL.  Be sure to include databaseURL option when calling firebase.initializeApp(). index.esm.js:77
[Show/hide message details.] Error: FIREBASE FATAL ERROR: Can't determine Firebase Database URL.  Be sure to include databaseURL option when calling firebase.initializeApp().
```

So firebase needs various values to connect to a specific database: `apiKey`, `authDomain`, `databaseURL`, `projectId` etc. There are no files where we are providing these values, so I'm a little confused. After doing a bit of looking around, it's kind of coming back to me.

We're using the `dotenv` module to provide environment variables from .env files. These were added to `.gitignore` so as to not expose the values so they weren't uploaded to the repository. The config file was trying to read from files that don't exist.

After adding the files from the other laptop, it still isn't working. Using just one .env file with dotenv works, but we were using two when following the course. This isn't recommended in the dotenv documentation, but that's a separate problem.

```javascript
// this doesn't work
if (process.env.NODE_ENV === 'test') {
   require('dotenv').config({ path: 'env.test' });
} else if (process.env.NODE_ENV === 'development') {
    require('dotenv').config({ path: 'env.development' });
}

// this does
require('dotenv').config();
```

Adding a `.` to each of the paths seems to have fixed the problem. Too simple, and an obvious mistake. I'm pretty sure it was working beforehand so I'm not sure what's changed. Perhaps the packages have changed in how the path is defined.

After all of this, the app loads fine and adding expenses seems to write to the database, but it doesn't fetch expenses to be displayed on the dashboard page.

Strange. I'll work on this next time.