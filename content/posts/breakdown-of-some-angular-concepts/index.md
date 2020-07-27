---
author: Arinze Okorji
title: Getting familiar with Angular
subtitle: Breakdown of Some Confusing Angular Concepts
date: 2020-07-26
description: A breakdown of some confusing concepts in Angular
categories:
  - Angular
  - Front-end development
banner:
  src: banner.svg
---

*They say Angular is difficult to learn; but is it? Here are basic concepts to get you up and running!*
<!--more-->

## Overview

When we are new to a field, skill, or what-have-you, things get very confusing very quickly; and Angular is no different. To help you on your journey to becoming an awesome Angular developer, here is a breakdown of a few commonly-confusing concepts. 

## Modules
A module in Angular refers to where components, services, directives, and pipes are grouped together. A module is a class with a @NgModule decorator and can import and export functionality to and from other modules. 

A simple application may consist of a single module, the root module, usually found in the app.module.ts file when the app is created by the Angular CLI. However, it is quite common to have multiple modules called feature modules when working with large applications. 

Feature modules help to separate unrelated parts of an application into different segments which makes it easier to debug as well as to compile - through lazy-loading (a way to only load different parts of your app only when needed instead of loading the entire app). For example, in an eCommerce app, we could have a ProductModule, CustomerModule, AdminModule.

{{< highlight ts >}}

import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component'


@NgModule({
declarations: [
AppComponent
],
imports: [
BrowserModule
],
providers: [],
exports: [],
bootstrap: [
AppComponent
]
})

export class AppModule{}

{{< /highlight >}}

The NgModule decorator takes an object with the following properties:

- declarations: This is an array of all components, directives, and pipes associated with a module. Every component, directive, or pipe must be added to a declarations array before Angular recognizes it.

- imports: This is an array of all the external modules a module requires to function properly. These external modules could be core Angular modules, e.g FormsModule, or 3rd-party modules.

- providers: This is an array of all the services associated with a particular module.

- exports: This is an array of all the components, directives, and pipes which would be made available to (other) feature modules who import the module.

- bootstrap: This is an array of the root component whos view would host all other views. Only the root NgModule should have a bootstrap property.


## Components

We can define an Angular component as a single block of logic which usually controls a patch of the screen. This logic is actually a typescript class that has the @Component decorator attached to it, and the patch of the screen is an HTML template, which the logic controls and (sometimes) populates with data, that gets rendered to the screen. 

{{< highlight js >}}

import { Component } from '@angular/core';

@Component({
selector: 'app-root',
template: `
<div> {{ title }} </div>
`,
style: [‘div: { font-size: 16px; }’]
})

export class AppComponent {
title: 'Angular Project';
}

{{< /highlight >}}

Components are usually set-up with a configuration object with the following properties:

- selector: This serves as the HTML tag used to nest a child component into a parent component’s template.

- template: This serves as the component’s inline template which is HTML. Alternatively, we could reference an external HTML template file with the templateUrl property. We, however, cannot use a template property and templateUrl property at the same time.

- style: This serves as the component’s style. It is an array of strings with each string-item being a CSS style. Alternatively, we could reference one or more external stylesheets by using a styleUrls property referencing an array of strings, with each string-item being a URL reference to a stylesheet. We, however, cannot use the style property and styleUrls property at the same time.


## Services

Let’s assume we have a function that we would like to use in two components in our Angular app. We could go ahead and create two implementations of the same function in our two components, and it would work as expected. However, this would not adhere to the Don’t-Repeat-Yourself (DRY) principle. 

Services help us solve this problem by housing component-independent code so the same code is easily available to the component(s) that need them.

{{< highlight js >}}

import { Injectable } from '@angular/core';
 
@Injectable({
  providedIn: 'root'
})
export class ModalService {
 
  constructor() { }
 
}


{{< /highlight >}}

A service is a class with a @Injectable decorator attached to it.

All services need to be added to the providers array of the module where it would be used. An alternative, however, is to add an optional object with a providedIn property to the @Injectable decorator and the property takes a string root.

## Directives

Angular directives give the template (HTML) more functionality by providing new syntax. There are three types of directives:

- Component directives - A component’s selector, when used to nest a component in a parent component, is referred to as a component directive. E.g

{{< highlight js >}}

<app-root></app-root>

{{< /highlight >}}

- Structural directives - These directives directly manipulate the DOM by creating or destroying the elements they are attached to. Common structural directives are *ngIf and *ngFor.

- Attribute directives - These directives manipulate the DOM by modifying its appearance and/or its behavior. Examples of a built-in attribute directive are NgStyle & NgClass.

## Pipes

Pipes enable us to transform displayed data.  It is a function that receives data and returns transformed data. Commonly used built-in pipes are:
- LowerCasePipe: Transforms text to all lower case.
- UpperCasePipe: Transforms text to all upper case.
- DatePipe: Formats a date value according to locale rules.
- CurrencyPipe: Transforms a number to a currency string, formatted according to locale rules.
- PercentPipe: Transforms a number to a percentage string, formatted according to locale rules.
- DecimalPipe: Transforms a number into a string with a decimal point, formatted according to locale rules.

To use a pipe, we first add  ` | `  followed by the name of the pipe. For example,

{{< highlight js >}}

import { Component, OnInit } from '@angular/core';

@Component({
selector: 'app-component',
template: `
<div> {{ title | lowercase }} </div>
`,
style: [‘div: { font-size: 16px; }’]
})

export class AppComponent implements OnInit {
title: 'Angular Project';
}

{{< /highlight >}}

The above code would give: ‘angular project’ because it has been transformed.



## Conclusion

The explanation above is a brief introduction to help any new-comer get familiar with some of Angular’s core concepts. Look out for upcoming posts with more detailed explanations and good luck with your learning!

