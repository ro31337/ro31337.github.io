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

{% gist bedece44e8cf9fd33ff8 %}

I have 4.x version of Node installed, you may have version 5.x - it doesn't matter too much for now. You have also just installed `gulp`. Some people prefer `grunt`, which is little bit more mature, but personally I prefer to configure things with code rather than using metadata - it's the main difference between `gulp` and `grunt`. With `grunt` you have to you json configuration. But we're already too far, let's initialize our project with Node.js.

### Creating new project with Node.js

Create and switch to directory:

{% gist 64de2194dc56e5514980 %}

Then type command to create `project.json`. I'll use default answers:

{% gist 86f56d3ad6e75ad64d83 %}

Congratulations, your `project.json` is ready! This is the main file where we'll keep all dependencies, configuration, test commands.

### More prerequisites

We're about to add new modules to our project. But before let's make sure we're good with few more prerequisites. You'll need:

* Essential build tools (XCode on Mac, `sudo apt-get install build-essential` on Linux, Visual Studio Redistributable C++ pain on Windows).
* Python 2.7.x - should be preinstalled on Mac OS X, type `python -v` to check.

### Adding few dependencies

`gulp` is absolutely required for us. We won't be able to run anything without it. And we must add it as dependency. Adding dependency means that other developers won't need to install everything globally. And will be able to use our packages as dependency locally. Locally means that these packages will be installed to directory `./node_modules` right in your application directory when you type `npm install`.

Let's do it:

{% gist ac8baee99a745341841a %}

With `--save-dev` option npm will add gulp to your development dependencies section inside of your `project.json`. You can add as many modules as you want, and folks from your team will be able to install them with just one command (`npm install`).

Now you'll have `node_modules` directory. It's probably a good time to add it to `./gitignore`:

{% gist 73f0e05b1c56c295d06a %}

At this point you'll have another file `npm-debug.log`. It's something to keep for your own debugging purposes, and I don't recommend to to include it in your repository, so let's ignore it too:

{% gist bb212b6a776862049b14 %}

### Start and test commands

For our purposes we'll add two commands: start and test. We should be able to use:

* `npm start` to kick off our development server locally, for development purposes
* `npm test` to run unit tests

We'll get back to `npm test` command in the next sections, let's add start command now. Update your `package.json` with the following code in `scripts` section:

{% gist 4c8d4fc87869037734ff %}

Don't forget to add comma after `"test"...`, you `package.json` should look like this now:

{% gist 1e9e714f7bf08ad5d7b5 %}

Let's type check if it works:

{% gist f0b7374c677436db404d %}

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

{% gist f13a9ed9b0893ade8677 %}

Move this file to `src` directory for now. We'll configure our development server for `src` directory, which is not good. You probably want to keep `src` directory for sources only. On the next steps, we'll reconfigure development server to `dist` directory.

Let's create our first `gulpfile.js`. If you're not familiar with gulp you may want to read "Getting started with Gulp" online. I won't cover gulp basics here.

{% gist 8aacbe0ec8594ee2e6c9 %}

Note that the code above is not ES6 yet. We've created the task with the name `server`. And in `package.json` we already have this:

{% gist 0116c77514258002ca6a %}

...so if we type:

{% gist 1210db6c3f412075f105 %}

...you'll have gulp's server task executed. It should give you an error:

{% gist b497d8a5f51f00440ef1 %}

Yes, module [gulp-server-livereload](https://www.npmjs.com/package/gulp-server-livereload) can't be found. We need to install it and add to our `package.json` as dependency:

{% gist c17867f650ece958957f %}

Run `npm start` again and... Congratulations! It works on `http://localhost:8000/`:

<img width="235" alt="78f33042-d974-41dc-9810-f776f9fe3b54" src="https://cloud.githubusercontent.com/assets/1477672/13264395/da011260-da22-11e5-9d81-587ab2c94bc4.png">

Here is my directory structure:

<img width="196" alt="2acd85be-ed6d-435d-927e-a1f7d9e62d52" src="https://cloud.githubusercontent.com/assets/1477672/13264413/f1544590-da22-11e5-90c5-54476438ab29.png">

I use Git and Atom editor, and green files above are new ones, yellow - modified since last commit, light gray - unmodified, dark gray - git-ignored.

Output from `npm start`:

{% gist 63e77ebd256c8e094ace %}

[Browse repository at this point of history](https://github.com/ro31337/react-boilerplate/tree/16d0a3fff4e7adb9b1863b4618a5802e2a78ec72)

### Adding very basic React component

Let's add very basic React component. Oops, we've just violated fundamental TDD principle - tests first. But here is the [wisdom](https://twitter.com/romanpushkin/status/690255031078006785):

> tdd or not tdd? My answer: if you already know how to do it, tdd! If you don't know - write the fucking code first.

Believe me, it's not hard to start with TDD right now. But for the sake of your own understanding I don't do it now. We need to configure a lot before we can even write our first test. So let's configure ES6 and React, and will get back to adding a test later.

I'll probably start with adding few more gulp tasks, so you won't be overwhelmed with a bunch of new stuff later. I'll reconfigure our development server to be serve files from `./dist` folder. For this we need to copy all of our html files from `./src` to `./dist` directory. Here is the gulp task for that:

{% gist 1c8034e88f2791c4013f %}

Running `gulp copy-html` will give you this output and create `dist` directory if it doesn't exist:

{% gist 6c7b82ca8e721ca455b2 %}

And now it's up to you if you want to add `dist` directory to your `.gitignore`. `./dist` will be something ready to use for us. Moments later we'll move our compiled version of React components there. And if you have `.less` or `.sass` - their compiled `.css` files will be there as well. For this boilerplate I'll add `./dist` to my `.gitignore`.

To make development server work we need to reconfigure it in our `gulpfile.js`:

{% gist 44ece7c72f8aee6afba7 %}

Well done, now we're serving files from `./dist`, but I have two caveats for you:

* If we change something in `./src` directory, it won't be live-reloaded anymore. We're serving files from `./dist` now.
* You have to type `gulp copy-html` now before you type `gulp server` (or `npm start`). You have to copy files to `./dist` folder now.

Using two commands are not very convenient. It's really easy to miss `gulp copy-html`. We need to automate it more. Let's tell gulp: "every time I run `gulp server`, execute `copy-html` task". Fortunately, it's really easy to do. We can inject dependency to our `server` task. You just need to provide array of tasks you want to be executed before your command:

{% gist 99fe0d96b1c6fdc2210c %}

It's super-cool, and no need to execute `gulp copy-html` anymore. But wait, I have another warning for you. Command above will work well, until you have something like this:

{% gist 855fe9289dc57789e87c %}

JavaScript has asynchronous nature. Everything in array will be executed asynchronously. Basically, for us this line means: "Before running `server` command run a bunch of other commands, don't wait for them to finish, just hope they'll be finished before our development server will serve the output of these commands". Side-effect is very interesting, and remains intermittent until your project grows really big: you will just notice that sometimes your React components generate errors on first load (or live reload), but never do it on the second reload. Just know that it's because gulp execute these commands asynchronously.

I this problem with [sequence](https://www.npmjs.com/package/run-sequence) module this way:

{% gist 0e9fe9d363c63c29f95f %}

I think I won't cover gulp tricks too much in this manual. Basically, the line above says: "execute `clean` command, then execute array of commands asynchronously, then execute final callback to tell gulp everything's finished). We'll add the sequence-thing in the next section.

Now our `gulpfile.js` looks like this:

{% gist 1516613aa5ef383329fd %}

Output from `gulp server` command:

{% gist 766d79e723ec8ce93f0b %}

Note that task `copy-html` is executed automatically when we type `gulp server`. It's done. But we have another issue: when you modify files in `./src` directory, you won't have your server live reloaded, because the server is watching for changes in `./dist` directory. To solve this we just need to do the following: when files are modified in `./src` directory, build them and copy to `./dist`.

### More Gulp magic

Let's agree on what are we going to have before doing any magic. We need the following:

* Before build, clean `./dist` directory.
* Build everything, which means: copy static html files, build React components (compile a bunch of `.jsx` files to one JavaScript file `bundle.js`) and copy them to `./dist`.
* Watch for changes in `./src` (!) folder. When changes are detected, just rebuild everything. Our development server is already configured to listen for changes in `./dist` and will reload the browser automatically. When we rebuild everything, everything gets copied to `./dist`.

### Cleaning build directory

Can be done with [gulp-rimraf](https://www.npmjs.com/package/gulp-rimraf). Clean task itself:

{% gist 89d80f166862b1fd2075 %}

And install the module:

{% gist 12dbeb4aba6df502b0d6 %}

Execute `clean` command:

{% gist 6a6b9fd7925e817e98a8 %}

Now we can clean `./dist` folder with one command.

### Adding a sequence to build

Let's think about the build process. What does it mean for us? If you come from C/C++ it can mean compile and link. If you came from Ruby it can mean nothing, because your programs are ready to use when you write them. But for us it means:

* Clean and wait until it's done (don't forget about asynchronous nature of JavaScript)
* Copy static html, convert React `.jsx` to `.js` (can be done asynchronously)
* Wait for all tasks to be done, because we rely on results of build process

JavaScript representation of sequence above will look like this:

{% gist 14a7bbed93de02932a66 %}

With using [run-sequence](https://www.npmjs.com/package/run-sequence) it will look like this:

{% gist 38e469923e013b774518 %}

And gulp task will look like this:

{% gist f32fc6683e6fc9e1531f %}

Now our `build` task depends on `clean` (it should be finished first), `copy-html`, and  `browserify`. Don't forget to install `run-sequence`:

{% gist 4d9c672b00351635ab28 %}

In your `gulpfile.js` you can place tasks at any place you want, in any order. But personally, I prefer to indent tasks. So if task `A` is depending on task `B` and I intent not to use `gulp B` command, I'll indent task `B` in my `gulpfile.js`. In our case I'll indent tasks `clean`, `copy-html`, and `browserify`. Let's run `build` command:

{% gist bc9a5852db6f17d4017f %}
Output is interesting and confirms what we were trying to achieve. We can see that `clean` command was finished first. Then two commands have started: `copy-html` and `browserify` at the same time. Then `browserify` has finished first (because it's empty now). And `copy-html` finished right after that. However, these results can be intermittent. If you have your `copy-html` command finished before `browserify` - it's okay, it just means that it can be finished before. Don't expect specific order for asynchronous commands.

Finally, we can make our `server` gulp command dependent on `build` instead of `copy-html` only:

{% gist ca4d9570ee4cc3025cf8 %}

Full version of `gulpfile.js` is little bit lengthy now:

{% gist 446497660da968cf7f92 %}

Output of `gulp server` (or `npm start`) command:

{% gist 4cc6ba3872ab25d450c6 %}

Just great, these two lines are really important here:

{% gist 8e50808f26161b798f41 %}

We are starting our development server after the build process. We shouldn't have any issues now. With the server running let's modify our `./src/index.html` to `Hello, world! 2`. It was not live reloaded in the browser. We still need to watch for changes in `./src` folder.

[Browse repository at this point of history](https://github.com/ro31337/react-boilerplate/tree/33c95d5b945e60038c25cf0c727d7c7d37e6b01c)

### Watch for changes

Gulp has plenty of plugins, and here is another one -- [gulp-watch](https://www.npmjs.com/package/gulp-watch):

{% gist e42cbe4406705998a121 %}

Now we can configure this plugin for our needs. But there is one thing to consider before we can do that. "Watch" means something should be started, and it should watch for changes. So it's not something you run and forget. You should have process/task/job running. Look at this code from our `gulpfile.js`:

{% gist 6b7e7985586ce187e631 %}

We pipe everything from `./dist` to `server`, which is `gulp-server-livereload` module. This module is executed, your console is blocked, because the server is running and serving files, and you need `Ctrl+C` to stop it. If you add watch task before (like if you add it to dependencies this way: `['build', 'watch']`), or after (by piping it next to `server`) - it won't work. In the first case development server won't start, in the second case watch task won't start.

So we need two tasks to be run at the same time: one will watch for changes, another will start development server. You can use undocumented `gulp.start`. It's not a good way to go, so I'm looking for your advice here. In Gulp 4 we should have these things improved and simplified, and we won't need `gulp.start` anymore or `sequence` thing. I would rewrite our server command this way:

{% gist 4fb440be4c1a3eaeebe4 %}

Now it should work. I have to admit that I found one interesting (well, expected) `gulp-server-livereload` behavior here. I had my `index.html` file with the following content:

{% gist 19c44a62ef7eb22b3a51 %}

I was too lazy to add more tags for the sake of just example, but it was my mistake.  `gulp-server-livereload` will not add magic javascript if you don't have closing `</body>` tag. Let's add it to our index.html:

{% gist 249998943cd359874dde %}

And run:

{% gist c3808745165ef9ec4f50 %}

Finally, you can change files in your `./src` directory and they'll be live reloaded.  `gulp-server-livereload` adds small javascript magic to every page right before closing `</body>` tag. Here is how the source of this page looks in my browser:

{% gist 71e38ba11e3586ac8ef4 %}

This JavaScript code loads `livereload.js`. Code from this file connects to your server app (your gulp task running development server). You'll see `Livereload client connected` message every time the client connected.

Try to modify your `./src/index.html` to see immediate changes in your browser. So we reached very important point (and have not covered any React stuff yet): now we can change our source, save the file, have it built in a second, and see immediate results in your browser. I think it's important and it really improves development experience. I wanted to cover this part only because very soon we'll rely on things like babelify, browserify, bundle and so on. You'll find yourself very tired of these basic operations without the proper automation. And now we have it. Let's add our first react component!

[Browse repository at this point of history](https://github.com/ro31337/react-boilerplate/tree/9a2d152b7cd4371bdaa62f573b465e58505911bb)

### Adding React component

We want to create very simple one. Something to start from. Greeter component will just greet you on the main page with hello message wrapped in `<h1>` tag. `./src/greeter.jsx` is our first file written with newer version of JavaScript -- ES6, and it's not purely JavaScript, it's JSX -- JavaScript mixed with XML. FYI: you still can use React with JavaScript if you want to. But in this article I won't cover this topic.

{% gist e38386fffabb1a0ebdac %}

Read [this article](http://babeljs.io/blog/2015/06/07/react-on-es6-plus) to learn more about ES6 and ES5 differences. On line 1 we import React namespace from `'react'` by using `import` keyword. On the last line we basically say "export what we have here to default namespace". There are other ways of exporting your classes, but let's just leave it this way.

Unfortunately you won't be able to attach this React file to your page without some magic. We just simply don't have support for JSX in our browsers. Moreover, it's not JSX + ES6, and we do not have ES6 support. And even if we'll have ES6 support, we'll need React library to be attached to our page, because JavaScript engine needs to know what the hell is `React.Component`. And even if it know what it is, this React component should be applied somewhere on the page. You may have one thousand of React components in memory, but do you want them to be displayed, and when you want them to be displayed?

I'll use helper ES6 file `app.jsx` to load and mount our component to DOM node with id `app`. Let's remove `Hello, world!` and add `app` node to `./src/index.html`:

{% gist 4058b05fbee87b3a71e7 %}

Our component from `./src/greeter.jsx` will be mounted to this node, but we still need `./src/app.jsx` helper (and entry point of our application):

{% gist e1710ac98835215fdd42 %}

Note that this file has no any class definitions and all the statements will be executed right away. We assign `greeter` variable, and on the last we mount it to our DOM node. If you don't understand this syntax, you may want to check official [Getting Started](https://facebook.github.io/react/docs/getting-started.html) guide. However, official manual looks little bit outdated (and not sexy to be honest).

My current directory structure:

<img width="193" alt="d336f19a-8251-4b3c-aa70-0e201a0e41e9" src="https://cloud.githubusercontent.com/assets/1477672/13264479/47fb724c-da23-11e5-88f0-f658622fabf9.png">

As I mentioned before, you can't just attach `./src/*.jsx` files with `<script>` tag to your page. Let's convert all of them to one file `bundle.js`. We'll need [browserify](http://browserify.org/).

> Browsers don't have the require method defined, but Node.js does. With Browserify you can write code that uses require in the same way that you would use it in Node.

Basically, it's very powerful tool that allows you to use any Node.js module in the browser. It's little bit more than that, and you can transform (from `.jsx` to `.js`), add source maps, combine few modules together, etc. Browserify has it's CLI, and can be executed both from [command line](https://github.com/substack/node-browserify#usage) and from your Node.js application (our `gulpfile.js`). Let's install it for our project:

{% gist 776067d4bf242b72e7c7 %}

We also should install [Babelify](https://github.com/babel/babelify) - transform module for Browserify to be executed at the top level. This module is just a wrapper around [Babel](https://github.com/babel/babel) compiler that converts your ES6 to JavaScript. Just a reminder: we need it until all the browsers can support ES6 natively. So our dependency chain looks like that:

{% gist 75a2d6fed01adc76a7d6 %}

We don't need to install Babel directly, only Babelify transform for Browserify:

{% gist 1667c2c1ee47e183c773 %}

There is one interesting note from [babelify](https://github.com/babel/babelify) GitHub repo:

> As of Babel 6.0.0 there are **no plugins included by default**.

Babel 6.0.0 has been [released](http://babeljs.io/blog/2015/10/29/6.0.0) on Oct 29, 2015. It is the main reason why your examples from the past won't work. I spent a day struggling with outdated examples, and repos, and so on.

There is also **extremely** helpful command from [babelify GitHub repo](https://github.com/babel/babelify#cli):

{% gist 808a733447c16ab07639 %}

(There is also Node.js example, I encourage you to go and take a look at these 6 lines). What the command above can tell us? We execute `browserify` with `-t` (transform) switch. We specify the name of transform module (`babelify`) and parameters to this transform module: `--presets [ es2015 react ]`. As it was mentioned before "no plugins included by default" in version 6.x of Babel (which is good I think), and we need to install and include them.

But before let's try to execute the command above without any installed plugins:

{% gist 43c49ef62ca0a5a45a23 %}

What do we see here? This part is really easy:

{% gist 003c7c62227d0355a712 %}

We execute `node_modules/.bin/browserify` - it's our Browserify tool installed locally. Then we pass path to our file(s), it's `src/**/*.jsx` in our case (all `.jsx` files in `src` directory, including subdirectories), and `-o` switch -- it's the output file (`bundle.js`).

Next chunk of magic line is `-t [ babelify --presets [ es2015 react ] ]`, just parameters to use babelify, and parameters for babelify to use presets. But we're getting

> Error: Couldn't find preset "es2015" relative to directory...

And for us it means that... "no plugins included by default". Yes, we need to install them:

{% gist e9740e6241bdb987e23b %}

Note that `babel-preset-react` is not `react` itself. Babel is about syntax transformations only. We also need to install `react` and `react-dom` libraries, because we have few `import` directives in our `jsx` files:

{% gist 92019e694631edd19469 %}

To make `React` and `ReactDOM` available in our namespace you need to install them the standard way:

{% gist 17b29eba1bf11889a582 %}

Let's execute magic command again:

{% gist 4bdf0103563f127ecf3b %}

I cheated. You'll get error message:

{% gist 7be01a31270d30061366 %}

Just wanted to follow problem-solution approach for better understanding. In `app.jsx` we use `import` directive:

{% gist 8011b62a3abf2df3c611 %}

And the module `'./greeter'` can't be imported. To fix this issue we should use `--extension` switch. Here is explanation from official docs:

> Consider files with specified EXTENSION as modules, this option can used multiple times.

And finally, the true version of our magic line should look like this:

{% gist dd4bd33167bf8e33213b %}

Now you should have `bundle.js` ready. Mine is 654 KB:

{% gist 4bb4bd37485d76b394dd %}

You can manually copy this file to your `./dist` folder and attach it with `<script>` tag:

{% gist bab72d646917c6beabdc %}

Be careful while copying, we have clean task in our build process. If you copy it first and then execute `gulp server`, you'll get your directory cleaned and you'll need to copy it again from another terminal window. We'll automate things in the next section.

Our React component works now:

<img width="251" alt="7308902f-97e0-460f-8a02-a268ffdd9657" src="https://cloud.githubusercontent.com/assets/1477672/13264509/615c8e10-da23-11e5-82c7-546236abc575.png">

[Here is the link to repository](https://github.com/ro31337/react-boilerplate/tree/1bbebf329f6266b02777fa906dd198d5c9fc08a0), but keep in mind that if you clone it, it won't work, because you won't have `bundle.js` in your `./dist` folder.

Our new dependencies in `package.json`:

<img width="352" alt="a540e717-b645-412c-b345-6830a24b35b8" src="https://cloud.githubusercontent.com/assets/1477672/13264578/ac3de6ea-da23-11e5-85ff-eefd9563cc94.png">

### Configure Gulp to do Browserify

To fully automate out build process let's convert this command:

{% gist f5540f238b932e2bfb09 %}

to Gulp task and then compare results. We already have empty `browserify` task. Browserify and Babelify are already installed for our app in `package.json`, so we can do:

{% gist 2172fafacdc171f80d84 %}

And in `browserify` task we return pre-configured browserify object:

{% gist 3deb0f226f2a1ac542d3 %}

However, it's not enough. You can run this task, but you won't see result file. It won't work because we still need code `-o` parameter from our command line example. Here is [browserify api example](https://github.com/substack/node-browserify#api-example):

{% gist dad5c94cff0b6704b0ec %}

Look at the last line. What is `bundle()`? Here is [another link to docs](https://github.com/substack/node-browserify#bbundlecb):

> Bundle the files and their dependencies into a single javascript file. Return a readable stream with the javascript file contents...

Okay, now it makes sense. Let's try to apply this knowledge to our example:

{% gist 07ee720c32545ab4c5d2 %}

Great! Now we can see output in the console. All we need to do now is to redirect "readable stream with the javascript file contents" to the file instead of `process.stdout`. After googling `gulp stream to file` I found two interesting links:

* [vinyl-source-stream -- use conventional text streams at the start of your gulp pipelines](https://www.npmjs.com/package/vinyl-source-stream)
* [Browserify + Transforms, official gulp recipe](https://github.com/gulpjs/gulp/blob/master/docs/recipes/browserify-transforms.md)

Official recipe is using `vinyl-source-stream` (the first link), and it seems the right choice. Basically, it this module redirects conventional text stream into gulp pipeline. So we can take our "readable stream with the javascript file contents" from `browserify`, and use this "conventional text stream at the start of our gulp pipeline" with `vinyl-source-stream`. Here is the source code based on examples:

{% gist a9ebae45112b0c5dbf1f %}
And how these changes look in our task:

{% gist 89429b7cc8f9033a3e24 %}

Execute gulp to test changes:

{% gist 6f4bca2e334520b10184 %}

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
