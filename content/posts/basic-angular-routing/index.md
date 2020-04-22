---
author: Arinze Okorji
title: Basic Routing In Angular
subtitle: How to setup basic routing in angular
date: 2020-04-21
description: A guide to setting up basic angular routing
categories:
  - Angular
  - Front-end development
  - Routing
banner:
  src: banner.svg
---

*Need help setting up routing for your angular project? Or simply need a refresher?*
<!--more-->

## Overview

Routing is a system that angular uses to enable in-app navigation between components.

It consists of an array of objects, with each object containing, at least, a path property indicating the URL definition for a route and a component property that indicates the component to be rendered when you navigate to the path.

## What we'll need
To get started, we would need to know the:
- Basics of components,
- Basics of templates,
- Angular command line interface (CLI),
- An Angular app (If you do not have one, we would be creating one using the CLI).



There are two methods for enabling routing in an application:
- Writing the code from scratch, or
- Using the Angular CLI

## Using the CLI
Typically, when creating a new angular project, we get prompted to either opt-in or opt-out of adding routing to our project. 

{{< figure src="option-to-add-routing-in-cmd.png" caption="Option to add routing to new Angular project" >}}

If we opt-in, angular generates the scaffold for a routing module, taking away the nuances of creating one ourselves. While this is great because it reduces development time, we still need to understand the whats, whys, whens and so on.

## Writing the code from scratch
First, in the command line terminal, let’s create a new project, along with a few components using the CLI. Note that we would not be enabling routing for this project using the CLI. We would add routing ourselves.

{{< highlight ts >}}

ng new <title-of-project>

{{< /highlight >}}

{{< highlight ts >}}

ng generate component first-component

{{< /highlight >}}

{{< highlight ts >}}

ng generate component second-component

{{< /highlight >}}


With our project created, we can now need to open our project folder in an IDE (I’ll be using Visual Studio Code (VS), however, you are free to use any IDE of your choice), and open the src/app/app.module.ts file.

In the app.module, we need to import the `RouterModule`. The `RouterModule` contains all the necessary directives, element tags, classes, and interfaces we would need for creating and using our routes.

{{< highlight ts >}}

import { RouterModule } from '@angular/router';

{{< /highlight >}}

After importing the `RouterModule`, we then need to create an array of type `Routes`. The `Routes` type needs to be imported as well.

{{< highlight ts >}}

import { RouterModule, Routes } from '@angular/router';

{{< /highlight >}}

{{< highlight ts >}}

const appRoutes: Routes = []

{{< /highlight >}}

The routes array would contain objects that represents route definitions. A basic route definition object would contain, at least, a path property, and a component property as shown below:

{{< highlight ts >}}

const appRoutes: Routes = [
{ path: '/path', component: FirstComponent },
{ path: ':id', component: SecondComponent },
{ path: '', redirectTo: '/path', pathMatch: 'full' },
{ path: '**', redirectTo: '/path' },
];

{{< /highlight >}}
*<small class='text-center'>Code snippet for AppModule</small>*

Now, let’s walk through the above code.

- /path: This is an absolute path which points to a component.

- /:id: This is referred to as a route a parameter. When we use the route parameter, we are telling Angular ‘hey, the :id value could be anything so look out for that’. Route parameters are useful for reusing a single component to display similar data; like a products’ details page. Note that to use a route parameter, we must include a colon before the parameter name.

- **: This is referred to as a wild card. It acts as a buffer for any route which has not been defined but has somehow been enter. What it means is that if we go to the URL bar and enter localhost:4200/blah-blah-blah, ‘blah-blah-blah’, which is not one of our defined paths, would get caught by our wild card path.

- The `redirectTo` property tells the router that if we encounter this defined path, redirect to this other path. The `pathMatch:'full'` tells the router that the whole URL path needs to match.

It is important to note that the order in which our routes are defined matters. This is because the router begins looking for the matching path at the top of our route definition list.

With our route definitions created, we need to tell Angular about them. We do that by adding the RouterModule we imported earlier to the import array as shown below.


{{< highlight ts >}}
// import statements

const appRoutes: Routes = [
{ path: '/path', component: FirstComponent },
{ path: ':id', component: SecondComponent },
{ path: '', redirectTo: '/path', pathMatch: 'full' },
{ path: '**', redirectTo: '/path' },
];

@NgModule({
// other stuff
imports: [
// other stuff
RouterModule.forRoot(appRoutes)
],
// other stuff
})

{{< /highlight >}}
*<small class='text-center'>Code snippet for AppModule</small>*

As you can see from the code above, we added a `forRoot()` method to the RouterModule. The `forRoot()` method tells Angular, ‘hey, the routes being added here are for the root module of this application’; and it is within the `forRoot()` method that we add our route definitions. 
When dealing with route definitions for lazy-loaded modules we use `forChild()`, but that is beyond the scope of this tutorial.

## Navigating using the route definitions
Now that we have our routes setup, how do we access the component we have navigated to? By adding routerLinks to our template and adding the router-outlet element where we want the routed component to be displayed.

{{< highlight ts >}}

// import statements

@Component({
// Other stuff
template: `
<router-outlet></router-outlet>
`
// Other stuff
})

export class AppComponent implements OnInit {
// Stuff go here
}

{{< /highlight >}}
*<small class='text-center'>Code snippet for AppComponent</small>*

From the above code, when the app first initializes, the `FirstComponent`, and subsequent routes' components would be displayed in place of the `router-outlet` element tag.

{{< highlight ts >}}


// import statements

@Component({
// Other stuff
template: `
<button routerLink=’/this-is-my-route-parameter’> Go to second component </button>
`
// Other stuff
})

export class FirstComponent implements OnInit {
// Stuff go here
}
{{< /highlight >}}
*<small class='text-center'>Code snippet for FirstComponent</small>*


{{< highlight ts >}}
// import statements

@Component({
// Other stuff
template: `
<button routerLink='/path'> Go to first component </button>
`
// Other stuff
})

export class SecondComponent implements OnInit {
// Stuff go here
}
{{< /highlight >}}
*<small class='text-center'>Code snippet for SecondComponent</small>*

The `routerLink` directive shown in the `FirstComponent` and `SecondComponent` binds a clickable HTML element to a route. Clicking on the element with a `routerLink` directive that is bound to a string or route parameter associated with the route definitions triggers navigation.


## Creating a separate Router Module

To help make our code more manageable, we have the option of separating our route definitions into its own module which we would call a router module. It is no different from a regular module file and looks like the code snippet below.

{{< highlight ts >}}

// other imports
import { RouterModule, Routes } from '@angular/router';

const appRoutes: Routes = [
{ path: '/path', component: FirstComponent },
{ path: ':id', component: SecondComponent },
{ path: '', redirectTo: '/path', pathMatch: 'full' },
{ path: '**', redirectTo: '/path' },
];

@NgModule({
// other stuff
imports: [
// other stuff
RouterModule.forRoot(appRoutes)
],
// other stuff
})

export class AppRouterModule { }

{{< /highlight >}}
*<small class='text-center'>Code snippet for AppRouterModule</small>*

Next, import the `AppRouterModule` into your `AppModule`.

{{< highlight ts >}}

// other imports
import { AppRouterModule } from '..path-to-app-router-module';

@NgModule({
// other stuff
imports: [
// other stuff
AppRouterModule
],
// other stuff
})

export class AppModule { }

{{< /highlight >}}

*<small class='text-center'>Code snippet for AppModule</small>*

With this, we have successfully created and implemented a basic route

## Conclusion
Routing is a fundamental part of Angular and is essential for providing users with an app-like experience. It is a powerful tool and is quite easy to implement. 

