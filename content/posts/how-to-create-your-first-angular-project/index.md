---
author: Arinze Okorji
title: How To Create Your First Angular Project
subtitle: Creating Your First Angular Project
date: 2020-03-22
description: A guide to creating an angular project
categories:
  - Angular
  - Front-end development
banner:
  src: Angular-logo.svg
---

*Hey there! New to Angular? Here’s a step-by-step guide to creating your first angular project. Shall we begin?*
<!--more-->

## Overview

Angular is an open-source platform for building mobile and desktop web applications. The first version, Angular 1.x (popularly known as AngularJs) was released in 2011. It was developed by two engineers  - Misko Hevery and Adam Abrons - and was later fully supported by Google. 

Although Angular 1.x, a JavaScript-based tool, was revolutionary for its time, it had a few performance issues that led the team behind the framework to reimagine the way the framework worked to overcome its problems, resulting in Angular 2.0. 

Angular 2 and later versions (collectively referred to as Angular) is TypeScript-based and is entirely different from AngularJs.


## What we'll need
To get started, we would need to install a few things first:
- The latest version of Node,
- Angular command-line interface (CLI),
- A code editor of your choice. I’ll use Visual Studio Code (VS code).


## Installing Node
Visit [nodejs.org](nodejs.org) and download the LTS version of Node.

{{< figure src="node-js-website.png" caption="Node.js website" >}}

Once the download is complete, open it and follow the installation guide.

## Installing the Angular CLI
After successfully installing node, we open a command-line terminal and enter 

{{< highlight js >}}

npm install -g @angular/cli.

{{< /highlight >}}

The -g flag ensures the angular CLI is installed globally on our machine.

## Creating our first angular project
Finally, its time to create our first angular project.

First, in our command-line terminal, we need to navigate to the folder in which we would create the project. Once there, we would enter the following:

{{< highlight js >}}

ng new <title-of-project>

{{< /highlight >}}

The above command creates a fully functional project which already follows Angular’s best practices.

## Serving our newly created project
To launch our newly created project, navigate into it using the command-line terminal, then run:

{{< highlight js >}}

ng serve

{{< /highlight >}}

The above command compiles and serves our project locally using Angular’s development server, making it accessible through any browser on our machine at localhost:4200. Visiting localhost:4200 on our browser should display this:

{{< figure src="angular-basic-scaffolding.png" caption="Basic Angular scaffolding served at localhost:4200" >}}

Note that this is the basic scaffolding that angular comes with when it is generated using the CLI and can be deleted.

## Angular project in our code editor

{{< figure src="angular-folder-in-vs-code.png" caption="Angular project in VS code" >}}

Here is our project in a code editor. Since I use VS code, the view of your project might be a little different. 

For now, all you need to know is that: 
- Most of the code we write would be within the src folder. 
- The assets folder stores static items like our images, icons, and fonts.
- The index.html file is essentially where our view is rendered. Since angular is a single-page application, the index.html file is the ‘single page’ which would simply get updated to display the appropriate view.
- The src/app/app.module.ts file is the root module of the application. Here, components, services, pipes, directives, and modules used in this module are declared. All that might be a bit confusing if you are absolutely new to angular. Read more on them here. 
- The src/app/app.component.ts is a component. Components are where specific logic is written in typeScript.
The src/app/app.component.html is where the HTML template for the app component is placed.
- The src/app/app.component.css is where the CSS for the app component is placed.

As our application grows, more components would be created to fulfill our needs.

## Conclusion
Although we used the CLI to generate our project, it is possible to create our angular app without the use of the CLI, but, as you can imagine, that would be cumbersome.
