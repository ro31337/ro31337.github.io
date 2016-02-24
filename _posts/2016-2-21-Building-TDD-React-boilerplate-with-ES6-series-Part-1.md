---
layout: post
title: Building TDD React boilerplate with ES6
comments: true
---

## Part 1. Introduction to Modern Frontend Workflow, adding our first React Component.

In this part we'll create our first React component, set our Front-end development environment with Node.js, create gulp build tasks, and will make sure things are running with latest JavaScript - ECMAScript 2015 (aka ES6). You can [browse Github repository](https://github.com/ro31337/react-boilerplate/tree/fcffd07bf381114bb1c3c90d37267621e3e91ae2) at the point of history for Part 1.

Why TDD React boilerplate and why just don't start with plenty of manuals already available online? Two reasons. First,  React has been updated from 0.13 to 0.14. Most of examples I found were outdated already. When it comes to TDD... this decision it's up to you. I've seen many mature developers who don't use TDD approach with React just because it seems that [front-end related stuff is not easy today](https://medium.com/@pistacchio/i-m-a-web-developer-and-i-ve-been-stuck-with-the-simplest-app-for-the-last-10-days-fb5c50917df#.nii0npcwp).

There are few ways to test React apps. What I really want to cover here is React unit testing. It's relatively easy to cover your React app with black box tests, but unit testing is something different. You need environment for that, you should be already familiar with a couple of tools and technologies to pick the best. And it took me about 2 days to build my own boilerplate that just works.

In 1-2 weeks by doing my React job and researching the subject I found that my boilerplate code can be improved more. I thought it can be really useful for all engineers who want to use React to read about my experience.

Let's start from basics.

### Installing Node.js

Yes, you need Node.js to be installed and configured before we can even start. And here is the first stumbling block. I head that many times (especially from Ruby devs): "But we are Rails developers, and Node.js is completely different technology, why do we need it?". Well, first of all - this is what we have now, you will need it. If you're unlucky and unfamiliar with basics of Node.js - you have to do it. Get ready to learn something new. There is no way to avoid that. But good news here is that you don't need to combine (it terms of coupling) two two technologies for the same project. You'll be good your with Rails or other framework, and you won't pollute development environment too much with bunch of new and sexy Node.js technologies. We'll talk about that later in "Using Bower" section.

Everything in this article is tested on Mac OS X. You shouldn't have issues configuring stuff for Linux. But when it comes to Windows - nobody knows. It can be pain I think. Moreover, if you're using Vagrant and Windows it can be pain. Node.js modules can be nested deeply in your directory structure and Windows host machine can stuck here. I had few errors related to that. There is 95% chance that you'll face issues with running Node with Vagrant+Windows. What you can do here is just to configure your Node application to be run somewhere else on your filesystem (not in shared directory like `/home/vagrant`).

If you have something to share, please share your thoughts/experience in comments. If you can provide a link to demo repository - please do.

And let's finally start. We'll start with major philosophical issue. Before you even install Node.js you have to decide how are you going to install global packages. There are two ways:

* Standard way: `sudo npm install something -g` (`-g` here means globally. You can also do `npm i` instead of `npm install`)
* The way I prefer: `npm install something -g` (without `sudo`).

I highly recommend to read a little bit about this issue and install Node.js with [this manual](https://gist.github.com/ro31337/8fd4b3dfc048dea13f94) (see "Solution" section). Let's check if it works:

```bash
$ node -v
v4.2.1
$ npm -v
3.5.3
$ npm install gulp -g # should be okay without sudo
```

I have 4.x version of Node installed, you may have version 5.x - it doesn't matter too much for now. You have also just installed `gulp`. Some people prefer `grunt`, which is little bit more mature, but personally I prefer to configure things with code rather than using metadata - it's the main difference between `gulp` and `grunt`. With `grunt` you have to you json configuration. But we're already too far, let's initialize our project with Node.js.

### Creating new project with Node.js

Create and switch to directory:

```bash
$ mkdir boilerplate && cd boilerplate
```

Then type command to create `project.json`. I'll use default answers:

```bash
$ npm init
```

Congratulations, your `project.json` is ready! This is the main file where we'll keep all dependencies, configuration, test commands.

### More prerequisites

We're about to add new modules to our project. But before let's make sure we're good with few more prerequisites. You'll need:

* Essential build tools (XCode on Mac, `sudo apt-get install build-essential` on Linux, Visual Studio Redistributable C++ pain on Windows).
* Python 2.7.x - should be preinstalled on Mac OS X, type `python -v` to check.

### Adding few dependencies

`gulp` is absolutely required for us. We won't be able to run anything without it. And we must add it as dependency. Adding dependency means that other developers won't need to install everything globally. And will be able to use our packages as dependency locally. Locally means that these packages will be installed to directory `./node_modules` right in your application directory when you type `npm install`.

Let's do it:

```bash
$ npm install gulp --save-dev
```

With `--save-dev` option npm will add gulp to your development dependencies section inside of your `project.json`. You can add as many modules as you want, and folks from your team will be able to install them with just one command (`npm install`).

Now you'll have `node_modules` directory. It's probably a good time to add it to `./gitignore`:

```bash
$ ls
node_modules package.json
$ echo node_modules >> .gitignore
```

At this point you'll have another file `npm-debug.log`. It's something to keep for your own debugging purposes, and I don't recommend to to include it in your repository, so let's ignore it too:

```bash
$ echo npm-debug.log >> .gitignore
```

### Start and test commands

For our purposes we'll add two commands: start and test. We should be able to use:

* `npm start` to kick off our development server locally, for development purposes
* `npm test` to run unit tests

We'll get back to `npm test` command in the next sections, let's add start command now. Update your `package.json` with the following code in `scripts` section:

```
    "start": "./node_modules/.bin/gulp server"
```

Don't forget to add comma after `"test"...`, you `package.json` should look like this now:

```
{
  "name": "boilerplate",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "./node_modules/.bin/gulp server"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp": "^3.9.1"
  }
}
```

Let's type check if it works:

```
$ npm start

> boilerplate@1.0.0 start /Users/Roman/work/boilerplate
> gulp server

[09:53:03] No gulpfile found
...
```

Our command works, and now it's a good time to add so-called gulpfile. What is this? Basically, it's JavaScript program. Nothing fancy, just the way to tell `gulp` what to do. Inside of gulpfile we usually have commands for our purposes. What kind of purposes we may have? It could be something like this:

* Start development server
* Copy files from one directory to another
* Convert less or sass files to css
* Concatenate files, etc.
* Minify JavaScript files
* Convert CoffeeScript to JavaScript

Gulp is very useful. Let's say you have jQuery plugin written with CoffeeScript. You want to release it with JavaScript in two files: minified and not minified, and you want to keep the source. You'll have two directories: `src` for source code, and `dist` for distribution package. You need one single command to convert from CoffeeScript to JavaScript, to copy files, to minify files, etc. You can look at [my very simple jQuery plugin](https://github.com/ro31337/jquery.ns-autogrow) repository to see how it works, and if you want to learn more about Gulp (note that gulpfile is written with CoffeeScript - I tend not to use it now and prefer ES6).

### Adding Gulpfile

The very first thing I would like to do is to create command for starting the server. It should just show us the very basic `index.html`:

```html
<body>
Hello, world!
```

Move this file to `src` directory for now. We'll configure our development server for `src` directory, which is not good. You probably want to keep `src` directory for sources only. On the next steps, we'll reconfigure development server to `dist` directory.

Let's create our first `gulpfile.js`. If you're not familiar with gulp you may want to read "Getting started with Gulp" online. I won't cover gulp basics here.

```javascript
var gulp = require('gulp');
var server = require('gulp-server-livereload');

gulp.task('server', function() {
  gulp
  .src('./src') // everything from ./src directory
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

Note that the code above is not ES6 yet. We've created the task with the name `server`. And in `package.json` we already have this:

```
"start": "./node_modules/.bin/gulp server"
```

...so if we type:

```
npm start
# or
./node_modules/.bin/gulp server
# or
gulp server # only if you did "npm install gulp -g" before
```

...you'll have gulp's server task executed. It should give you an error:

```
Error: Cannot find module 'gulp-server-livereload'
```

Yes, module [gulp-server-livereload](https://www.npmjs.com/package/gulp-server-livereload) can't be found. We need to install it and add to our `package.json` as dependency:

```bash
$ npm install gulp-server-livereload --save-dev
```

Run `npm start` again and... Congratulations! It works on `http://localhost:8000/`:

<img width="235" alt="78f33042-d974-41dc-9810-f776f9fe3b54" src="https://cloud.githubusercontent.com/assets/1477672/13264395/da011260-da22-11e5-9d81-587ab2c94bc4.png">

Here is my directory structure:

<img width="196" alt="2acd85be-ed6d-435d-927e-a1f7d9e62d52" src="https://cloud.githubusercontent.com/assets/1477672/13264413/f1544590-da22-11e5-90c5-54476438ab29.png">

I use Git and Atom editor, and green files above are new ones, yellow - modified since last commit, light gray - unmodified, dark gray - git-ignored.

Output from `npm start`:

```
$ npm start

> boilerplate@1.0.0 start /Users/Roman/work/boilerplate
> gulp server

[06:23:07] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:23:07] Starting 'server'...
[06:23:07] Livereload started at http://localhost:35729
[06:23:07] Finished 'server' after 15 ms
[06:23:07] Webserver started at http://localhost:8000
```

TODO: browse my git repository at this point (16d0a3ff).

### Adding very basic React component

Let's add very basic React component. Oops, we've just violated fundamental TDD principle - tests first. But here is the [wisdom](https://twitter.com/romanpushkin/status/690255031078006785):

> tdd or not tdd? My answer: if you already know how to do it, tdd! If you don't know - write the fucking code first.

Believe me, it's not hard to start with TDD right now. But for the sake of your own understanding I don't do it now. We need to configure a lot before we can even write our first test. So let's configure ES6 and React, and will get back to adding a test later.

I'll probably start with adding few more gulp tasks, so you won't be overwhelmed with a bunch of new stuff later. I'll reconfigure our development server to be serve files from `./dist` folder. For this we need to copy all of our html files from `./src` to `./dist` directory. Here is the gulp task for that:

```javascript
gulp.task('copy-html', function() {
  return gulp // note "return" keyword
    .src('./src/**/*.html') // copy everything, including subdirectories
    .pipe(gulp.dest('dist')); // to dist folder
});
```

Running `gulp copy-html` will give you this output and create `dist` directory if it doesn't exist:

```
[06:46:54] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:46:54] Starting 'copy-html'...
[06:46:54] Finished 'copy-html' after 16 ms
```

And now it's up to you if you want to add `dist` directory to your `.gitignore`. `./dist` will be something ready to use for us. Moments later we'll move our compiled version of React components there. And if you have `.less` or `.sass` - their compiled `.css` files will be there as well. For this boilerplate I'll add `./dist` to my `.gitignore`.

To make development server work we need to reconfigure it in our `gulpfile.js`:

```javascript
gulp.task('server', function() {
  gulp
  .src('./dist') // everything from ./dist directory <-- MODIFIED HERE
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

Well done, now we're serving files from `./dist`, but I have two caveats for you:

* If we change something in `./src` directory, it won't be live-reloaded anymore. We're serving files from `./dist` now.
* You have to type `gulp copy-html` now before you type `gulp server` (or `npm start`). You have to copy files to `./dist` folder now.

Using two commands are not very convenient. It's really easy to miss `gulp copy-html`. We need to automate it more. Let's tell gulp: "every time I run `gulp server`, execute `copy-html` task". Fortunately, it's really easy to do. We can inject dependency to our `server` task. You just need to provide array of tasks you want to be executed before your command:

```javascript
gulp.task('server', ['copy-html'], function() { // <-- ARRAY ADDED BETWEEN 1st and 3rd PARAMETER
```

It's super-cool, and no need to execute `gulp copy-html` anymore. But wait, I have another warning for you. Command above will work well, until you have something like this:

```javascript
gulp.task('server', ['sass', 'copy-html', 'copy-css', 'something', 'blabla'], function() {
```

JavaScript has asynchronous nature. Everything in array will be executed asynchronously. Basically, for us this line means: "Before running `server` command run a bunch of other commands, don't wait for them to finish, just hope they'll be finished before our development server will serve the output of these commands". Side-effect is very interesting, and remains intermittent until your project grows really big: you will just notice that sometimes your React components generate errors on first load (or live reload), but never do it on the second reload. Just know that it's because gulp execute these commands asynchronously.

I this problem with [sequence](https://www.npmjs.com/package/run-sequence) module this way:

```javascript
var sequence = require('run-sequence');
// ...
gulp.task('build', function(callback) {
  sequence('clean', ['sass', 'copy-html', 'copy-css', 'something', 'blabla'], callback);
});
```

I think I won't cover gulp tricks too much in this manual. Basically, the line above says: "execute `clean` command, then execute array of commands asynchronously, then execute final callback to tell gulp everything's finished). We'll add the sequence-thing in the next section.

Now our `gulpfile.js` looks like this:

```javascript
var gulp = require('gulp');
var server = require('gulp-server-livereload');

gulp.task('copy-html', function() {
  return gulp // note "return" keyword
    .src('./src/**/*.html') // copy everything, including subdirectories
    .pipe(gulp.dest('dist')); // to dist folder
});

gulp.task('server', ['copy-html'], function() {
  gulp
  .src('./dist') // everything from ./dist directory
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

Output from `gulp server` command:

```
$ gulp server
[06:28:16] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:28:16] Starting 'copy-html'...
[06:28:16] Finished 'copy-html' after 16 ms
[06:28:16] Starting 'server'...
[06:28:16] Livereload started at http://localhost:35729
[06:28:16] Finished 'server' after 9.65 ms
[06:28:16] Webserver started at http://localhost:8000
```

Note that task `copy-html` is executed automatically when we type `gulp server`. It's done. But we have another issue: when you modify files in `./src` directory, you won't have your server live reloaded, because the server is watching for changes in `./dist` directory. To solve this we just need to do the following: when files are modified in `./src` directory, build them and copy to `./dist`.

### More Gulp magic

Let's agree on what are we going to have before doing any magic. We need the following:

* Before build, clean `./dist` directory.
* Build everything, which means: copy static html files, build React components (compile a bunch of `.jsx` files to one JavaScript file `bundle.js`) and copy them to `./dist`.
* Watch for changes in `./src` (!) folder. When changes are detected, just rebuild everything. Our development server is already configured to listen for changes in `./dist` and will reload the browser automatically. When we rebuild everything, everything gets copied to `./dist`.

### Cleaning build directory

Can be done with [gulp-rimraf](https://www.npmjs.com/package/gulp-rimraf). Clean task itself:

```javascript
var clean = require('gulp-rimraf');
// ...
// Clean dist folder
gulp.task('clean', function() {
  return gulp.src('./dist', {
    read: false
  }).pipe(clean({
    force: true
  }));
});
```

And install the module:

```bash
$ npm install gulp-rimraf --save-dev
```

Execute `clean` command:

```bash
$ gulp clean
[06:15:30] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:15:30] Starting 'clean'...
[06:15:30] Finished 'clean' after 18 ms
```

Now we can clean `./dist` folder with one command.

### Adding a sequence to build

Let's think about the build process. What does it mean for us? If you come from C/C++ it can mean compile and link. If you came from Ruby it can mean nothing, because your programs are ready to use when you write them. But for us it means:

* Clean and wait until it's done (don't forget about asynchronous nature of JavaScript)
* Copy static html, convert React `.jsx` to `.js` (can be done asynchronously)
* Wait for all tasks to be done, because we rely on results of build process

JavaScript representation of sequence above will look like this:

```javascript
'clean', ['copy-html', 'browserify'], callback
```

With using [run-sequence](https://www.npmjs.com/package/run-sequence) it will look like this:

```javascript
var sequence = require('run-sequence');
// ...
sequence('clean', ['copy-html', 'browserify'], callback);
```

And gulp task will look like this:

```javascript
var sequence = require('run-sequence');
// ...
gulp.task('build', function(callback) {
  sequence('clean', ['copy-html', 'browserify'], callback);
});
// convert React components to be used in the browser
gulp.task('browserify', function() {
  // nothing here for now
});
```

Now our `build` task depends on `clean` (it should be finished first), `copy-html`, and  `browserify`. Don't forget to install `run-sequence`:

```bash
$ npm install run-sequence --save-dev
```

In your `gulpfile.js` you can place tasks at any place you want, in any order. But personally, I prefer to indent tasks. So if task `A` is depending on task `B` and I intent not to use `gulp B` command, I'll indent task `B` in my `gulpfile.js`. In our case I'll indent tasks `clean`, `copy-html`, and `browserify`. Let's run `build` command:

```
$ gulp build
gulp build
[06:42:02] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:42:02] Starting 'build'...
[06:42:02] Starting 'clean'...
[06:42:02] Finished 'clean' after 12 ms
[06:42:02] Starting 'copy-html'...
[06:42:02] Starting 'browserify'...
[06:42:02] Finished 'browserify' after 6.92 μs
[06:42:02] Finished 'copy-html' after 9.83 ms
[06:42:02] Finished 'build' after 23 ms
```
Output is interesting and confirms what we were trying to achieve. We can see that `clean` command was finished first. Then two commands have started: `copy-html` and `browserify` at the same time. Then `browserify` has finished first (because it's empty now). And `copy-html` finished right after that. However, these results can be intermittent. If you have your `copy-html` command finished before `browserify` - it's okay, it just means that it can be finished before. Don't expect specific order for asynchronous commands.

Finally, we can make our `server` gulp command dependent on `build` instead of `copy-html` only:

```javascript
gulp.task('server', ['build'], function() {
// ...
```

Full version of `gulpfile.js` is little bit lengthy now:

```javascript
var gulp = require('gulp');
var server = require('gulp-server-livereload');
var clean = require('gulp-rimraf');
var sequence = require('run-sequence');

gulp.task('build', function(callback) {
  sequence('clean', ['copy-html', 'browserify'], callback);
});

  // Clean dist folder
  gulp.task('clean', function() {
    return gulp.src('./dist', {
      read: false
    }).pipe(clean({
      force: true
    }));
  });

  // copy html files
  gulp.task('copy-html', function() {
    return gulp // note "return" keyword
      .src('./src/**/*.html') // copy everything, including subdirectories
      .pipe(gulp.dest('dist')); // to dist folder
  });

  // convert React components to be used in the browser
  gulp.task('browserify', function() {
    // nothing here for now
  });

gulp.task('server', ['build'], function() {
  gulp
  .src('./dist') // everything from ./dist directory
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

Output of `gulp server` (or `npm start`) command:

```
$ gulp server
[06:50:41] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:50:41] Starting 'build'...
[06:50:41] Starting 'clean'...
[06:50:41] Finished 'clean' after 18 ms
[06:50:41] Starting 'copy-html'...
[06:50:41] Starting 'browserify'...
[06:50:41] Finished 'browserify' after 9.41 μs
[06:50:41] Finished 'copy-html' after 8.17 ms
[06:50:41] Finished 'build' after 28 ms
[06:50:41] Starting 'server'...
[06:50:41] Livereload started at http://localhost:35729
[06:50:41] Finished 'server' after 11 ms
[06:50:41] Webserver started at http://localhost:8000
```

Just great, these two lines are really important here:

```
[08:50:41] Finished 'build' after 28 ms
[08:50:41] Starting 'server'...
```

We are starting our development server after the build process. We shouldn't have any issues now. With the server running let's modify our `./src/index.html` to `Hello, world! 2`. It was not live reloaded in the browser. We still need to watch for changes in `./src` folder.

TODO: link to commit 33c95d5b

### Watch for changes

Gulp has plenty of plugins, and here is another one -- [gulp-watch](https://www.npmjs.com/package/gulp-watch):

```bash
$ npm install gulp-watch --save-dev
```

Now we can configure this plugin for our needs. But there is one thing to consider before we can do that. "Watch" means something should be started, and it should watch for changes. So it's not something you run and forget. You should have process/task/job running. Look at this code from our `gulpfile.js`:

```javascript
gulp.task('server', ['build'], function() {
  gulp
  .src('./dist') // everything from ./dist directory
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

We pipe everything from `./dist` to `server`, which is `gulp-server-livereload` module. This module is executed, your console is blocked, because the server is running and serving files, and you need `Ctrl+C` to stop it. If you add watch task before (like if you add it to dependencies this way: `['build', 'watch']`), or after (by piping it next to `server`) - it won't work. In the first case development server won't start, in the second case watch task won't start.

So we need two tasks to be run at the same time: one will watch for changes, another will start development server. You can use undocumented `gulp.start`. It's not a good way to go, so I'm looking for your advice here. In Gulp 4 we should have these things improved and simplified, and we won't need `gulp.start` anymore or `sequence` thing. I would rewrite our server command this way:

```javascript
var watch = require('gulp-watch');

// ...

gulp.task('watch', function() {
  // rebuild when jsx, js, html change, in src directory, including subdirectories
  watch('./src/**/*.{jsx,js,html}', function() {
    gulp.start('build');
  });
});

// already existing "server" task

gulp.task('server', ['build'], function() {
  gulp.start('watch'); // 1 LINE ADDED FOR "server" TASK
  gulp
  .src('./dist') // everything from ./dist directory
  .pipe(server({ // pipe to server object
    livereload: true, // reload when files are changed
    open: true // and open the browser
  }));
});
```

Now it should work. I have to admit that I found one interesting (well, expected) `gulp-server-livereload` behavior here. I had my `index.html` file with the following content:

```html
<body>
Hello, world!
```

I was too lazy to add more tags for the sake of just example, but it was my mistake.  `gulp-server-livereload` will not add magic javascript if you don't have closing `</body>` tag. Let's add it to our index.html:

```html
<body>
Hello, world!
</body>
```

And run:

```bash
$ gulp server
```

Finally, you can change files in your `./src` directory and they'll be live reloaded.  `gulp-server-livereload` adds small javascript magic to every page right before closing `</body>` tag. Here is how the source of this page looks in my browser:

```html
<body>
Hello, world!
<script type="text/javascript">var _lrscript = document.createElement('script');_lrscript.type = 'text/javascript';_lrscript.defer = _lrscript.async = true;_lrscript.src = 'http://' + ((null||location.host).split(':')[0]) + ':35729/livereload.js?';document.body.appendChild(_lrscript);</script></body>
```

This JavaScript code loads `livereload.js`. Code from this file connects to your server app (your gulp task running development server). You'll see `Livereload client connected` message every time the client connected.

Try to modify your `./src/index.html` to see immediate changes in your browser. So we reached very important point (and have not covered any React stuff yet): now we can change our source, save the file, have it built in a second, and see immediate results in your browser. I think it's important and it really improves development experience. I wanted to cover this part only because very soon we'll rely on things like babelify, browserify, bundle and so on. You'll find yourself very tired of these basic operations without the proper automation. And now we have it. Let's add our first react component!

TODO: repository at 9a2d152b

### Adding React component

We want to create very simple one. Something to start from. Greeter component will just greet you on the main page with hello message wrapped in `<h1>` tag. `./src/greeter.jsx` is our first file written with newer version of JavaScript -- ES6, and it's not purely JavaScript, it's JSX -- JavaScript mixed with XML. FYI: you still can use React with JavaScript if you want to. But in this article I won't cover this topic.

```jsx
import React from 'react';

class Greeter extends React.Component {
  render() {
    return <h1>Hello!</h1>;
  }
}

export default Greeter;
```

Read [this article](http://babeljs.io/blog/2015/06/07/react-on-es6-plus) to learn more about ES6 and ES5 differences. On line 1 we import React namespace from `'react'` by using `import` keyword. On the last line we basically say "export what we have here to default namespace". There are other ways of exporting your classes, but let's just leave it this way.

Unfortunately you won't be able to attach this React file to your page without some magic. We just simply don't have support for JSX in our browsers. Moreover, it's not JSX + ES6, and we do not have ES6 support. And even if we'll have ES6 support, we'll need React library to be attached to our page, because JavaScript engine needs to know what the hell is `React.Component`. And even if it know what it is, this React component should be applied somewhere on the page. You may have one thousand of React components in memory, but do you want them to be displayed, and when you want them to be displayed?

I'll use helper ES6 file `app.jsx` to load and mount our component to DOM node with id `app`. Let's remove `Hello, world!` and add `app` node to `./src/index.html`:

```html
<body>
<div id="app"></div>
</body>
```

Our component from `./src/greeter.jsx` will be mounted to this node, but we still need `./src/app.jsx` helper (and entry point of our application):

```jsx
import ReactDOM from 'react-dom';
import React from 'react';
import Greeter from './greeter';

let greeter = (
  <Greeter />
)

ReactDOM.render(greeter, document.getElementById('app'));
```

Note that this file has no any class definitions and all the statements will be executed right away. We assign `greeter` variable, and on the last we mount it to our DOM node. If you don't understand this syntax, you may want to check official [Getting Started](https://facebook.github.io/react/docs/getting-started.html) guide. However, official manual looks little bit outdated (and not sexy to be honest).

My current directory structure:

<img width="193" alt="d336f19a-8251-4b3c-aa70-0e201a0e41e9" src="https://cloud.githubusercontent.com/assets/1477672/13264479/47fb724c-da23-11e5-88f0-f658622fabf9.png">

As I mentioned before, you can't just attach `./src/*.jsx` files with `<script>` tag to your page. Let's convert all of them to one file `bundle.js`. We'll need [browserify](http://browserify.org/).

> Browsers don't have the require method defined, but Node.js does. With Browserify you can write code that uses require in the same way that you would use it in Node.

Basically, it's very powerful tool that allows you to use any Node.js module in the browser. It's little bit more than that, and you can transform (from `.jsx` to `.js`), add source maps, combine few modules together, etc. Browserify has it's CLI, and can be executed both from [command line](https://github.com/substack/node-browserify#usage) and from your Node.js application (our `gulpfile.js`). Let's install it for our project:

```bash
$ npm install browserify --save-dev
```

We also should install [Babelify](https://github.com/babel/babelify) - transform module for Browserify to be executed at the top level. This module is just a wrapper around [Babel](https://github.com/babel/babel) compiler that converts your ES6 to JavaScript. Just a reminder: we need it until all the browsers can support ES6 natively. So our dependency chain looks like that:

```
Browserify --> Babelify --> Babel
```

We don't need to install Babel directly, only Babelify transform for Browserify:

```bash
$ npm install babelify --save-dev
```

There is one interesting note from [babelify](https://github.com/babel/babelify) GitHub repo:

> As of Babel 6.0.0 there are **no plugins included by default**.

Babel 6.0.0 has been [released](http://babeljs.io/blog/2015/10/29/6.0.0) on Oct 29, 2015. It is the main reason why your examples from the past won't work. I spent a day struggling with outdated examples, and repos, and so on.

There is also **extremely** helpful command from [babelify GitHub repo](https://github.com/babel/babelify#cli):

```bash
$ browserify script.js -o bundle.js -t [ babelify --presets [ es2015 react ] ]
```

(There is also Node.js example, I encourage you to go and take a look at these 6 lines). What the command above can tell us? We execute `browserify` with `-t` (transform) switch. We specify the name of transform module (`babelify`) and parameters to this transform module: `--presets [ es2015 react ]`. As it was mentioned before "no plugins included by default" in version 6.x of Babel (which is good I think), and we need to install and include them.

But before let's try to execute the command above without any installed plugins:

```
$ node_modules/.bin/browserify src/**/*.jsx -o bundle.js -t [ babelify --presets [ es2015 react ] ]
Error: Couldn't find preset "es2015" relative to directory...(error truncated)
```

What do we see here? This part is really easy:

```
node_modules/.bin/browserify src/**/*.jsx -o bundle.js
```

We execute `node_modules/.bin/browserify` - it's our Browserify tool installed locally. Then we pass path to our file(s), it's `src/**/*.jsx` in our case (all `.jsx` files in `src` directory, including subdirectories), and `-o` switch -- it's the output file (`bundle.js`).

Next chunk of magic line is `-t [ babelify --presets [ es2015 react ] ]`, just parameters to use babelify, and parameters for babelify to use presets. But we're getting

> Error: Couldn't find preset "es2015" relative to directory...

And for us it means that... "no plugins included by default". Yes, we need to install them:

```bash
$ npm install babel-preset-es2015 babel-preset-react --save-dev
```

Note that `babel-preset-react` is not `react` itself. Babel is about syntax transformations only. We also need to install `react` and `react-dom` libraries, because we have few `import` directives in our `jsx` files:

```jsx
import ReactDOM from 'react-dom';
import React from 'react';
```

To make `React` and `ReactDOM` available in our namespace you need to install them the standard way:

```bash
$ npm install react react-dom --save-dev
```

Let's execute magic command again:

```bash
$ node_modules/.bin/browserify src/**/*.jsx -o bundle.js -t [ babelify --presets [ es2015 react ] ]
```

I cheated. You'll get error message:

```
Error: Cannot find module './greeter' from '
```

Just wanted to follow problem-solution approach for better understanding. In `app.jsx` we use `import` directive:

```jsx
import Greeter from './greeter';
```

And the module `'./greeter'` can't be imported. To fix this issue we should use `--extension` switch. Here is explanation from official docs:

> Consider files with specified EXTENSION as modules, this option can used multiple times.

And finally, the true version of our magic line should look like this:

```bash
$ node_modules/.bin/browserify --extension=.jsx src/**/*.jsx -o bundle.js -t [ babelify --presets [ es2015 react ] ]
```

Now you should have `bundle.js` ready. Mine is 654 KB:

```
$ ls -lh
total 1328
-rw-r--r--    1 Roman  home   654K Feb 21 11:43 bundle.js
drwxr-xr-x    3 Roman  home   102B Feb 20 14:57 dist
-rw-r--r--    1 Roman  home   1.3K Feb 21 14:54 gulpfile.js
drwxr-xr-x  444 Roman  home    15K Feb 20 11:32 node_modules
-rw-r--r--    1 Roman  home   611B Feb 21 11:32 package.json
drwxr-xr-x    5 Roman  home   170B Feb 20 16:53 src
```

You can manually copy this file to your `./dist` folder and attach it with `<script>` tag:

```
<body>
<div id="app"></div>
<script type="text/javascript" src="bundle.js"></script>
</body>
```

Be careful while copying, we have clean task in our build process. If you copy it first and then execute `gulp server`, you'll get your directory cleaned and you'll need to copy it again from another terminal window. We'll automate things in the next section.

Our React component works now:

<img width="251" alt="7308902f-97e0-460f-8a02-a268ffdd9657" src="https://cloud.githubusercontent.com/assets/1477672/13264509/615c8e10-da23-11e5-82c7-546236abc575.png">

Here is the link to repository: TODO 1bbebf32, but keep in mind that if you clone it, it won't work, because you won't have `bundle.js` in your `./dist` folder.

Our new dependencies in `package.json`:

<img width="352" alt="a540e717-b645-412c-b345-6830a24b35b8" src="https://cloud.githubusercontent.com/assets/1477672/13264578/ac3de6ea-da23-11e5-85ff-eefd9563cc94.png">

### Configure Gulp to do Browserify

To fully automate out build process let's convert this command:

```bash
$ node_modules/.bin/browserify --extension=.jsx src/**/*.jsx -o bundle.js -t [ babelify --presets [ es2015 react ] ]
```

to Gulp task and then compare results. We already have empty `browserify` task. Browserify and Babelify are already installed for our app in `package.json`, so we can do:

```javascript
var browserify = require('browserify');
var babelify = require('babelify');
```

And in `browserify` task we return pre-configured browserify object:

```javascript
var babelifyConfig = babelify.configure({presets: ['es2015', 'react']});

return browserify({
    entries: 'src/app.jsx',
    extensions: ['.jsx']
  })
  .transform(babelifyConfig) // the same as -t command line parameter
```

However, it's not enough. You can run this task, but you won't see result file. It won't work because we still need code `-o` parameter from our command line example. Here is [browserify api example](https://github.com/substack/node-browserify#api-example):

```
var browserify = require('browserify');
var b = browserify();
b.add('./browser/main.js');
b.bundle().pipe(process.stdout);
```

Look at the last line. What is `bundle()`? Here is [another link to docs](https://github.com/substack/node-browserify#bbundlecb):

> Bundle the files and their dependencies into a single javascript file. Return a readable stream with the javascript file contents...

Okay, now it makes sense. Let's try to apply this knowledge to our example:

```
  gulp.task('browserify', function() {
    var babelifyConfig = babelify.configure({presets: ['es2015', 'react'], extensions: ['.jsx']});

    return browserify({
        entries: 'src/app.jsx', // the same as "--extension=.jsx src/**/*.jsx"
        extensions: ['.jsx']
      })
      .transform(babelifyConfig) // the same as -t command line parameter
      .bundle()
      .pipe(process.stdout);
  });
```

Great! Now we can see output in the console. All we need to do now is to redirect "readable stream with the javascript file contents" to the file instead of `process.stdout`. After googling `gulp stream to file` I found two interesting links:

* [vinyl-source-stream -- use conventional text streams at the start of your gulp pipelines](https://www.npmjs.com/package/vinyl-source-stream)
* [Browserify + Transforms, official gulp recipe](https://github.com/gulpjs/gulp/blob/master/docs/recipes/browserify-transforms.md)

Official recipe is using `vinyl-source-stream` (the first link), and it seems the right choice. Basically, it this module redirects conventional text stream into gulp pipeline. So we can take our "readable stream with the javascript file contents" from `browserify`, and use this "conventional text stream at the start of our gulp pipeline" with `vinyl-source-stream`. Here is the source code based on examples:

```javascript
var source = require('vinyl-source-stream');
// ...
      .transform(babelifyConfig) // the same as -t command line parameter
      .bundle() // "readable stream with the javascript file contents"
      .pipe(source('bundle.js')) // "conventional text stream at the start of our gulp pipeline", wrapped with "pipe" method
      .pipe(gulp.dest('dist')); // standard gulp way to redirect gulp pipe to directory
```
And how these changes look in our task:

```javascript
  // convert React components to be used in the browser
  gulp.task('browserify', function() {
    var babelifyConfig = babelify.configure({presets: ['es2015', 'react'], extensions: ['.jsx']});

    return browserify({
        entries: 'src/app.jsx', // the same as "--extension=.jsx src/**/*.jsx"
        extensions: ['.jsx']
      })
      .transform(babelifyConfig) // the same as -t command line parameter
      .bundle() // "readable stream with the javascript file contents"
      .pipe(source('bundle.js')) // "conventional text stream at the start of our gulp pipeline", wrapped with "pipe" method
      .pipe(gulp.dest('dist/bundle.js')); // standard gulp way to redirect gulp pipe to directory
  });
```

Execute gulp to test changes:

```
$ gulp browserify
[06:48:59] Using gulpfile ~/work/boilerplate/gulpfile.js
[06:48:59] Starting 'browserify'...
[06:49:01] Finished 'browserify' after 1.59 s
$ ls -lh ./dist
total 1312
-rw-r--r--  1 Roman  home   654K Feb 23 09:49 bundle.js
```

Congratulations! Our `browserify` gulp task is already the part of  `build` task. And it means that we can just `npm start`, modify our `greeter.jsx` in our favorite code editor and see immediate changes in the browser! Under the hood gulp will clean `dist` folder, will babelify ES6 to ES5, will browserify our modules, and will copy html files. Nice work!

<img width="501" alt="2d9e6a2a-d93a-4841-a714-844890e2c313" src="https://cloud.githubusercontent.com/assets/1477672/13264628/e0a93c72-da23-11e5-82b0-c285281287c5.png">

At the screenshot above I showed you the moment when I saved new file. You can see that `build` task has been started automatically.

[Browse repository at this point of history for Part 1](https://github.com/ro31337/react-boilerplate/tree/fcffd07bf381114bb1c3c90d37267621e3e91ae2).

### All parts

* [Part 1. Introduction to Modern Frontend Workflow, adding our first React Component] (http://ro31337.github.io/Building-TDD-React-boilerplate-with-ES6-series-Part-1/)
* Part 2. Setting up tests. Using React Test Utilities. (coming soon)
* Part 3. Adding more functionality to React component and adding tests.  (coming soon)
* Part 4. Switching to Enzyme.  (coming soon)
* Part 5. Bower and integration with Rails.  (coming soon)

### About the author:

[Roman Pushkin](http://twitter.com/romanpushkin) is Web Expert from San Francisco. Roman has over 10 years of web development experience with variety of technologies and frameworks.

TODO: donate button
TODO: comments in blog
