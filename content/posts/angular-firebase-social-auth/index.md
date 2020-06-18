---
author: Arinze Okorji
title: Integrate Firebase Social Authentication In Angular
subtitle: How to integrate firebase social authentication in angular
date: 2020-06-11
description: A guide to setting up firebase's social auth in an angular app
categories:
  - Angular
  - Front-end development
banner:
  src: banner.svg
---

*Let's integrate Firebase social auth in our Angular app*
<!--more-->


For this tutorial, we’ll enable Google and Facebook sign-in methods with firebase.

# Firebase Setup
First, go to the firebase console and add a project.

Navigate to Authentication Panel > **Sign-in method**.

Under the Sign-in method panel, there would be a list of disabled providers. We would first enable Google auth since it is easier.


## Enabling Google Auth:
Select the Google folder and tap the disable switch.

{{< figure src="enable-google-auth-provider-in-firebase.png" caption="Enable Google auth provider in firebase" >}}

## Enabling Facebook Auth:
First, create an app on Facebook for Developers and get its App Id and App Secret.
Next, back in the firebase Sign-in panel, select Facebook, tap the disable switch, and input the App Id and App Secret from Facebook for developers into their appropriate fields.

{{< figure src="enable-facebook-auth-provider-in-firebase.png" caption="Enable Facebook auth provider in firebase" >}}

Next, to complete set up, copy Oauth redirect URL provided to your Facebook app configuration.

# Angular Setup
To configure our authentication on our Angular app, we need to

- Download and install node

- Open our terminal and install the latest (or near latest) version of Angular (Command-line interface) CLI

{{< highlight ts >}}

npm install -g @angular/cli

{{< /highlight >}}

- Create a new Angular project using the CLI

{{< highlight ts >}}

ng new firebase-authentication-app

{{< /highlight >}}

- Create auth service where our authentication logic would reside

{{< highlight ts >}}

ng g service auth

{{< /highlight >}}

- Install @angular/fire and Firebase package using npm. @angular/fire is what we would use to connect our Angular application to the firebase app we created earlier. The Firebase package contains methods that would help to interact with Firebase.

{{< highlight ts >}}

npm install firebase @angular/fire --save

{{< /highlight >}}

Now that our app is set up, we need to go back to our settings page of the Firebase console and locate the Your app section. Select the app we created. Copy the firebaseConfig object.

{{< figure src="firebaseConfig-in-firebase.png" caption="The firebaseConfig object in Firebase" >}}

Next, we need to go back to our Angular app folder and locate src/app/environment.ts. Within this folder, paste the `FirebaseConfig` object.

{{< figure src="firebaseConfig-in-angular.png" caption="The firebaseConfig object in Angular app" >}}

Now, in our app.module, we need to add AngularFireModule to the `imports` array of our `@NgModule`. When doing so, we need to call the initializeApp method present in the AngularFireModule, passing in the `FirebaseConfig` object from the environment.ts file as an argument.

Also, add `AngularFireAuthModule` to the imports array.

{{< highlight ts >}}

// other imports
import { AngularFireModule } from '@angular/fire';
import { AngularFireAuthModule } from '@angular/fire/auth';
import { environment } from '../environments/environment';

@NgModule({
  declarations: [AppComponent],
  imports: [
    // Other modules
    AngularFireModule.initializeApp(environment.firebaseConfig),
    AngularFireAuthModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})

{{< /highlight >}}

                                       *****


Next, let’s go to our `AuthService`. This is where most of the magic would happen.

Import the following:

{{< highlight ts >}}

import { auth } from 'firebase/app';
import { AngularFireAuth } from '@angular/fire/auth'

{{< /highlight >}}

Inject the following:

{{< highlight ts >}}

constructor(
    public angularFireAuth: AngularFireAuth, // Inject AngularFireAuth
    private router: Router, // For routing to another page after authenticating
    private ngZone: NgZone // For running Angular code in promise
  ) {}

{{< /highlight >}}

### Sign in with Google

Let’s create a loginWithGoogle() method. This would enable us to authenticate with a Google account.

The routes array would contain objects that represents route definitions. A basic route definition object would contain, at least, a path property, and a component property as shown below:

{{< highlight ts >}}


loginWithGoogle() {
    return new Promise<any>((resolve, reject) => {
      const provider = new auth.GoogleAuthProvider();
      provider.addScope('profile');
      provider.addScope('email');

      // Initiate signInWithRedirect method
      this.initiateSignInWithRedirect(provider);
      resolve();
    });
  }
 
initiateSignInWithRedirect(provider) {
    this.angularFireAuth.auth.signInWithRedirect(provider);
  }
{{< /highlight >}}

**Explanation:** To enable Firebase to know it is the Google authentication provider we would be using, we need to create a `new auth.GoogleAuthProvider()` object, which we assigned to the `const provider`.

Next, we call the `initiateSignInWithRedirect()` method, passing in the provider constant as an argument. This calls `this.angularFireAuth.auth.signInWithRedirect(provider);` 
This method causes the Angular app to redirect to the authentication provider’s page (in our case, Google’s) where authentication occurs.

The authentication process is a promise. While we wait for the promise to resolve, we get redirected back to our app. While back at our app, we would need to wait for the authentication to resolve. 

Once the authentication promise resolves, we would need to know so that we can perform tasks accordingly. Thankfully, the AngularFireAuth ships with a method to help us with that:

{{< highlight ts>}}

this.angularFireAuth.auth.getRedirectResult()

{{< / highlight >}}

This method is a promise. Chaining a then method to it helps us get the authenticated user’s info as a response object, as well as run other codes (which could be navigating to a home page?). 

{{ < highlight ts >}}
this.angularFireAuth.auth
        .getRedirectResult()
        .then((result) => {
          if (result.user) {

            // Using NgZone as the only way to run Angular-specific code within Promise
            this.ngZone.run(() => {
              this.router.navigate(['/home']);
            });
          }
        })
        .catch((err) => {
          console.log(err);
        });

{{ < /highlight >}}

We use `NgZone` here to run Angular-specific code within a promise. This is because promises are not part of Angular’s core functionality. Therefore, Angular-specific code will not run directly in them without `NgZone`.

Let’s place `this.angularFireAuth.auth.getRedirectResult()` in our constructor. That way when we get redirected back to our Angular application, it would get triggered. So we have this:

{{< highlight ts >}}
constructor(
    public angularFireAuth: AngularFireAuth, // Inject AngularFireAuth
    private router: Router,
    private ngZone: NgZone
  ) {
      this.angularFireAuth.auth
        .getRedirectResult()
        .then((result) => {
          if (result.user) {
            // Using NgZone as the only way to run Angular-specific code within Promise
            this.ngZone.run(() => {
              this.signInPendingState = false;
              this.setReporterLoggedInStatus();
              this.router.navigate(['/home']);
            });
          }
        })
        .catch((err) => {
          console.log(err);
        });
  }
{{< / highlight >}}

### Sign in with Facebook
Everything applicable to the Google sign-in is applicable to Facebook sign in - with one exception, we assign `new auth.FacebookAuthProvider()` to our provider.

**Note:** An alternative to `this.angularFireAuth.auth.signInWithRedirect(provider)` is `this.angularFireAuth.auth.signInWithPopup(provider)`. This method simply creates a popup window to access the social auth provider’s page instead of redirecting our app to it.


## Logging out
The  AngularFireAuth package ships with a signout method which is a promise. This signs a user out and we can chain a then method to run additional code.

{{< highlight ts >}}
logout() {
    this.angularFireAuth.auth
      .signOut()
      .then(() => {
          // User is signed out
          this.router.navigate(['/']);
      })
      .catch((err) => {
        console.log(err);
      });
  }
{{< /highlight >}}

Now our AuthService should look like this:

{{< highlight ts >}}
import { Injectable, NgZone } from '@angular/core';
import { auth } from 'firebase/app';
import { AngularFireAuth } from '@angular/fire/auth';
import { Router } from '@angular/router';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  constructor(
    public angularFireAuth: AngularFireAuth, // Inject AngularFireAuth
    private router: Router, // For routing to another page after authenticating
    private ngZone: NgZone // For running Angular code in promise
  ) {
      this.angularFireAuth.auth
        .getRedirectResult()
        .then((result) => {
          if (result.user) {
            // Using NgZone as the only way to run Angular-specific code within Promise
            this.ngZone.run(() => {
              this.signInPendingState = false;
              this.setReporterLoggedInStatus();
              this.router.navigate(['/home']);
            });
          }
        })
        .catch((err) => {
          console.log(err);
        });
  }

  loginWithGoogle() {
    return new Promise<any>((resolve, reject) => {
      const provider = new auth.GoogleAuthProvider();

      // Initiate signInWithRedirect method
      this.initiateSignInWithRedirect(provider);
      resolve();
    });
  }

  loginWithFacebook() {
    return new Promise<any>((resolve, reject) => {
      const provider = new auth.FacebookAuthProvider();
      // Initiate signInWithRedirect method
      this.initiateSignInWithRedirect(provider);
      resolve();
    });
  }

  logout() {
    this.angularFireAuth.auth
      .signOut()
      .then(() => {
          this.router.navigate(['/']);
      })
      .catch((err) => {
        console.log(err);
      });
  }

  initiateSignInWithRedirect(provider) {
    this.angularFireAuth.auth.signInWithRedirect(provider);
  }
}
{{< /highlight >}}


## Conclusion
Firebase Social Auth is easy to implement in an Angular app. Taking away the complexity of creating a custom backend for such a service, we are free to build other exciting features of out app!

