---
author: "Arinze Okorji"
title: "How To Use JS Promises"
subtitle:
date: 2020-02-28
categories: 
  - javascript
  - Front-end Development
banner: 
 src: banner.png
---

*This guide is aimed to help newcomers get familiar with the basic concepts for working with javascript promises.*
<!--more-->

## Introduction
A promise is an asynchronous operation that returns a value or an error. Unlike synchronous operations that go step-by-step from top to bottom, we usually do not know when promises would be completed. 

This period of incompletion is referred to as a *pending state* of a promise.

Once a promise is completed, it enters a *settled state*.

A settled promise does one of two things: 
- **Resolves**, returning a value or
- **Rejects**, returning the reason for an error

Now how do we actually create a promise? We do that using the Promise constructor. For example:

{{< highlight js >}}

let myPromise = new Promise((resolve, reject) => {
// write your code here
setTimout(resolve(‘value’), 1000)
})

{{< /highlight >}}

In the above example, after 1 second, the promise would resolve to return the string ‘value’.

## Promise.prototype.then()
To access the resolved value (or rejection error) from myPromise, we would need to use a `then()` method to our promise.
The `then()` method is used when a promise is settled and we need to work on the value it resolved. The `then()` method returns a promise and takes two optional parameters which are:
onFulfillment callback function which executes if the promise is fulfilled. It takes in one argument which represents the resolved value
onRejection callback function which executes if the promise is rejected. It takes in one argument which represents the error

Continuing from the previous example, we would work on the resolved value like so:

{{< highlight js >}}
myPromise.then(res => {
console.log(res) // logs ‘value’
})
{{< /highlight >}}

**Note:**  The above code does not include the optional onRejection callback function.

## Chaining
Chaining is a term used to describe the act of using multiple `then()` methods on a single promise. This is done if we need to further use the value returned from the initial `then()` method. 

The amount of `then()` methods which can be chained depends on the need of the project being worked on. Each `then()` method is executed in the order they are inserted; but for the next `then()` method to get the value from previous `then()` method in the chain, the previous one must return the value so it gets passed on to the next.

For example,

{{< highlight js >}}
let newPromise = new Promise((resolve, reject) => {
 setTimeout(resolve(1), 1000);
})


newPromise
.then(res => {
  res = res + 1;
  console.log(res) // 2
  return res;
})
.then(res => {
  res = res + 1;
  console.log(res) // 3
  return res;
})
.then(res => {
  res = res + 1;
  console.log(res) // 4
  return res;
})
{{< /highlight >}}


## Promise.prototype.catch()
To handle errors in our promise, we simply need to add a `catch()` method to the chain. The `catch()` method returns a promise and deals with rejected cases only.

{{< highlight js >}}

newPromise
.then(res => {
  res = res + 1;
  throw new Error(res)
})
.catch(err => {
  console.log(err) // 2
})

{{< /highlight >}}

A chain can be made after a failure (`catch()` method) as it may be useful to run operations if an action failed in the chain. For example

{{< highlight js >}}

newPromise
.then(res => {
  console.log(‘Something’);
  throw new Error(‘Error’);
console.log(‘Something else’);
})
.catch(err => {
  console.log(err)
})
.then(res => {
  console.log(‘Something more’);
})
{{< /highlight >}}

The above code would print out:

{{< highlight js >}}

// Something
// Error
// Something more

{{< /highlight >}}

**Note:** ‘Something else’ was not printed because the code block execution was terminated when the error was thrown.


## Conclusion
Promises are a great way of handling asynchronous code. This tutorial is by no means a comprehensive guide to the subject because it is aimed at newcomers. Feel free to reference the [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) guide for deeper concepts.
