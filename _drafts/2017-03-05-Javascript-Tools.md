---
layout: post
title: "Javascript Tools"
categories: Javascript
tags: Web Dev
---

* content
{:toc}

### Javascript Tools

https://dev.to/kayis/the-tools-of-the-js-trade




Why Tools?
They are created to make our life easier. But at the cost, that we need to understand how they work.

For example, as a good Web developer, you know that every request, even the smallest, takes some time to reach your user, so you could get the idea to bundle your JavaScript files into one, so only one request will be made to get it.

Less overhead, faster loading times, etc. But without a tool, you would have to manually open every JS file and copy its content into one big file or you would have to write all your code into one big file right from the beginning. Both suboptimal approaches to the problem. So you download or write yourself a bundler that does all the ugly work for you.

Also they can add quality checking tools like Linters, that can help to improve your code and catch some errors you might made.

So, we could argue that tooling, even in JavaScript land is a good thing.

Why No Tools?
The problem with all the tools is, they are software and they need to be understood. Some of them add new code or even modify existing code, which is not always obvious to the average developer (I'm considering myself one, hopefully at least average D:)

They add complexity and for getting started this complexity can be a no-go.

Who Forces The Tools On You?
Now the JavaScript ecosystem goes ones step further and effectively requires these tools for specific frameworks or libraries. Okay, effectively is maybe a strong word, but working without these tools is often not as nice as it could be...

For example React, which is often used in combination with a syntax extension called JSX. If you want to write all the cool XML inside JS, that makes React code look so future and hip, you need a tool that can turn this back into regular JavaScript, before it can hit the browser. Also, React used ES2015 features before they were readily implemented in all browsers.

Yes, React had a way to use this without it and I even wrote a small tutorial that explains how to use React with ES5, but most of the examples and tutorials online required at least one ES2015 and/or JSX.

So you want to learn a new JavaScript framework and now you also have to learn a build tool that turns ES2015+JSX into ES5. That's a rather steep learning curve. Which is kinda sad, since React has not much concepts and a rather tiny API.

What Are The Tools?
Okay, so now that we established that tools can help you and some frameworks require them to work effectively. What are the tools available in JavaScript land? And what do they do?

I can't tell you all of them, because JavaScript developers just love to create new things every day, even tools.

But I will try to write a bit about those I know. I guess, since I shipped a few Web apps in my time, I will have met enough to get started.

First, all of these tools run in Node.js, so if you want to setup a JavaScript tool-chain it's basically a bunch of Node.js modules installed via some package manager and run locally (or on your CI-Server) via CLI.

Package Manager

There are a bunch of package managers. Most of them build on the npm repositories. That means, even if you don't use npm, you probably end up with libraries from their repos.

npm The default package manger of Node.js, biggest repo.
Bower A package manger made for the browser, has its own repo independant of npm.
Yarn A new package manager that fixes some issues of NPM, got much traction lately. Also uses npm repo.
ied Also an alternative to npm, tries to incorporate ideas of the Nix package manger to JavaScript land. Uses npm repos.
pnpm A bit like ied but with better integration into the npm workflow, faster than npm , but uses the npm repos.
Task Runner

If you got tools, why not get tools to run your tools? Joke aside, if you got a few things to do, you want to coordinate them somehow and not always type the whole commands with all the arguments and stuff. Think Make for JS. Oh and can also use Make ;)

npm Has a script attribute in its package.json that can be used to define some simple tasks. Has access to CLIs installed in dev-dependencies, so they don't need to be installed globally.
Grunt First of its kind in JavaScript land, used if the npm-scripts aren't enough anymore.
Gulp Like Grunt, but needs less configuration.
Compiler

If you want to use all the fancy new syntax extension, like JSX, of today or you need some strong typing, like FlowType or TypeScript, you are forced to use a compiler that turns your code back into something that a browser understands.

Babel started as a project for ES2015, but now supports JSX, FlowType and all the new ECMAScript features, even the "unreleased" ones.
Buble a fast ES2015 compiler, if you don't need all the fancy features of Babel.
TSC the TypeScript compiler. JSX and most of the new ES2015 features are part of TypeScript. As the name suggests, it comes with a type system.
Bundler

I don't know if bundler is the right word, since most of these tools don't just do bundling. They also minify or modify source in different ways.

Browserify was created to make the synchronous CommonJS modules of Node.js available in asynchronous browser land. So it doesn't simply merge all your files, but also wrapps modules for async use.
Webpack a bundler that can read basically all Web-dev related files and bundle them by itself. Wants to coordinate all the asset loading, so the browser hasn't to guess what to do.
Rollup Explicitly created for ES2015 modules, uses tree shaking for dead code elimination, which results in smaller builds.
Closure Compiler a highly sophisticated bundler, that uses type annotation comments to make all sorts of optimisations to your code, harder to integrate than Rollup, but results in very tiny builds.
Code Quality

Some of the compilers mentioned above are already some kind of code quality tools, because of their type systems. But there are other ways.

JSLint a very opinionated tool that tells if you wrote junk code. Will hurt your feelings.
JSHint like JSLint, but with more config options, so your feelings are safe.
ESLint like JSHint, but pluggable(?) well I saw it as better to configure than JSHint.
Prettier think gofmt for JavaScript. Convert your code into an AST and back into text, based on some opinionated style rules. Fixes many of the errors the linters find (and don't fix) for you in the process.
TSLint basically ESLint for TypeScript.
Testing

As good developers, we all love TDD and BDD amirite? ;) So there are many tools that can help you accomplish this task.

Mocha the standard test framework, I guess? Very extendable.
Jasmine like Mocha but for BDD.
AVA a futuristic test runner, whatever that means. Anyway it is good for high concurrency and parallelism testing.
Tape a simple test runner that uses the tap protocoll, if that is your thing.
Jest has extra nice features for UI testing, like snapshots of the HTML produced, that can be compared etc.
Bonus
http-server a npm CLI tool that runs a http-server in the current directory. Very helpful for simple front-end projects.
What to use?
Depends on what you need.

I'm a front-end developer and often start with npm, Webpack and http-server.

Npm gives me the ability to install 3rd party libs and define some scripts, for example running Webpack or the http-server.

Webpack has loaders and plugins for all kind of file-types, JavaScript, CSS, Less, SASS, TypeScript, etc. pp. When I need some of these, I just install them and add them to my webpack config and go on. Often the loaders are simply wrappers that integrate the compilers I mentioned above into the Webpack pipeline, so I don't have to deal with the compilers directly.

For proof of concepts I often use content delivery networks like unpkg so I don't have to install anything. Unpkg gives HTTP access to all packages in the npm repository.

Just a HTML file with some <script> tags to get started.

Conclusion
Well, it would be nice if there were more tutorials that did step back and try to explain how things work on a basic level, before they abstract it away behind some sophisticated tool.

But if you want to build fast and reliable Web applications, you need some of these tools or write them yourself.