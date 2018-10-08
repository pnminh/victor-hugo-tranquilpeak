---
title: Setup Angular Development Environment
thumbnailImage: /images/uploads/angular_test.png
thumbnailImagePosition: left
coverImage: /images/uploads/angular_test.png
metaAlignment: center
coverMeta: out
date: '2018-10-08T10:58:46-05:00'
tags:
  - blog
  - post
categories:
  - blog
---
Recently our team started our endeavor with Front-end development using Angular Framework. Since the project began from scratch, we use Angular CLI to start our boilerplate codes. I myself have used VS Code for a while to play around with React and some static code generators for my blog posts. However, as a team is more familiar with IntelliJ as we work with Java all the time, and based on the fact that JetBrains does have a very famous product for Front-end development called WebStorm, we decided to setup Angular app with our mighty IDE. This post shows some of my findings setting up IntelliJ for Angular development. If time allows, I will have another blog for VS Code as well.

First we need to create an Angular module for our project workspace and point it to the app that we already created with Angular CLI. I tried with the option to import existing module but I could not find a way to let IntelliJ import the whole project as Angular one.

Select New Module > Static Web > Angular CLI.

![](/images/uploads/screen-shot-2018-10-08-at-12.14.50-pm.png)

Then select the root project folder in the Module file location.

![](/images/uploads/screen-shot-2018-10-08-at-12.17.24-pm.png)

After creating the module, IntelliJ tried to run ng new to create a new project but since we already have one we can just ignore the error.

The next part is to enable TSLint for code analysis(https://palantir.github.io/tslint/). Go to Preferences > Languages & Frameworks > TypeScript > TSLint, enable TSLint and select the source package for it. Since the project generated a tslint.json file, we can choose automatic search or manually select the file from our project.

![](/images/uploads/screen-shot-2018-10-08-at-12.32.21-pm.png)

Unit tests and code coverage are one of the most important requirements for TDD. Luckily, IntelliJ support Karma Test Runner. We need to install the Karma plugin to IntelliJ in order to setup Karma tests. Once it's installed, we can setup run config for Karma to run all tests. Angular app already created the karma.conf.js for us. The only note is here that the karma package should be part of @angular/cli package for it to run angular test cases successfully.

![](/images/uploads/screen-shot-2018-10-08-at-12.36.33-pm.png)

From here, we can select Run Karma Tests with coverage to see how good our tests are.

That's it for today. Happy coding.
