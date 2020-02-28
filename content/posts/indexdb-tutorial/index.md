---
author: Arinze Okorji
title: IndexedDB (Getting Started)
subtitle: How to use indexedDB
date: 2020-02-17
description: This is a beginner's guide to using the indexedDB API
categories:
  - JavaScript
  - Front-end development
banner:
  src: banner.png
---

*IndexedDb is a great way to store persistent data on the browser. This guide aims to introduce the basic concepts of the technology to help you started.*
<!--more-->


## Overview

IndexedDB is an asynchronous browser API used to store data that persists in a user’s browser and is accessible offline. It comes in handy if you are a front-end developer who needs to store data without any back-end knowledge or when building progressive web apps.

IndexedDB is object-oriented, meaning it requires the data to be stored as JavaScript objects.

**Note:** IndexedDb is event-based. Meaning you will be working with asynchronous calls.

The basic format for using indexedDB is to:
1. Open a database
2. Create an object store
3. Perform CRUD operations to the created database using transactions
4. Use the results of CRUD.

## Opening a database

Before opening a database, it is important to ensure browser support for the indexedDB.


{{< highlight js >}}
const databaseVersion = 1;
let taskDatabasePromise;
let taskDatabase;

if(!('indexedDB' in window)) {
    return;
} else {
    taskDatabasePromise = indexedDB.open('to-do-list', dataBaseVersion);
}
{{< /highlight >}}

The indexedDB.open() method takes two arguments: 
the name of the database and
the version number of the database

The version number of a newly created indexedDB database is the integer 1. Databases can have only one version number at a time. To upgrade a database, increment the database version number.

**Note:** When you upgrade the database, the onupgradeneeded callback function can be called. It is only within this callback function that the database schema can be managed using the event handler.

After creating your DB, there are few things you could do when the callback resolves ( remember, it is asynchronous ). You could:
- Make an onerror call if the process failed,

{{< highlight js >}}
taskDatabasePromise.onerror = (event) => {
    taskDatabase = event.target.result;
}
{{< /highlight >}}

- Make an onsuccess call if the process was successful,

{{< highlight js >}}
taskDatabasePromise.onsuccess = (event) => {
    taskDatabase = event.target.result;
}
{{< /highlight >}}

- Make an onupgradeneeded call. Object stores are created here but more on that later.

{{< highlight js >}}
taskDatabasePromise.onupgradeneeded = (event) => {
    taskDatabase = event.target.result;
}
{{< /highlight >}}

It is only within the callback function you get hold of the database in question. So most (if not all) of the code you’ll write to interact with the database would be within any of the above calls.

**Note:** If you want to access an already existing database, open a database with the same name and version number as the one you need. If it exists, you would be able to access it within the onsuccess function call.

## What are object stores and why do I need them?

When you create a database, you can store data directly into it, there’s no rule against that. But to help you easily sort and retrieve data, your best bet is to create an object-store. According to [MDN](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore), 

“Records within an object store are sorted according to their keys. This sorting enables fast insertion, look-up, and ordered retrieval.”

Object stores can only be created within the onupgradeneeded callback function using the db.createObjectStore() like so:


{{< highlight js >}}
taskDatabasePromise.onupgradeneeded = (event) => {
    taskDatabase = event.target.result;
    if(!taskDatabase.objectStoreNames.contains('Tasks')) {
        taskDatabase.createObjectStore('Tasks', {autoIncrement: true})
    }
}
{{< /highlight >}}

The first parameter is the name of the object store, and a second parameter is an options object with optional key path or autoincrements properties. 

AutoIncrement, set to false by default, when set to true provides the object store with a key generator (a mechanism for producing new keys in an ordered sequence Gain more info about key generators).

The key path tells the browser which item from the inserted data to use as the key for the inserted data. E.g if I was to insert objects that all had a name property, I could declare my key path like so:


{{< highlight js >}}
// Using a key path

taskDatabasePromise.onupgradeneeded = (event) => {
    taskDatabase = event.target.result;
    if(!taskDatabase.objectStoreNames.contains('Tasks')) {
        taskDatabase.createObjectStore('Tasks', {keyPath: 'name'})
    }
}

{{< /highlight >}}

Using key paths helps you query the object store more efficiently.

**Note:** When using key path, every object inserted should include the property which acts as the key path.
Find more information on key paths [here](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_keypath).

## What are transactions?

CRUD operations to your database can only be done through an operation called transactions. 

Transactions are made on the database by calling db.transaction() and specifying the mode of operation (‘readonly’ or ‘readwrite’). “readonly” is used when you only retrieve data and “readwrite” is used when you modify the database.

The target object store is then referenced by the transaction like so:

{{< highlight js >}}
taskDatabasePromise.onsuccess = (event) => {
    taskDatabase = event.target.result;
    const tx = taskDatabase.transaction('Tasks', 'readonly')
    const objectStore = tx.objectStore('Tasks')
}
{{< /highlight >}}


### What are transaction verbs?

Basic verbs include, but not limited to
- add() used for inserting new items into the database.
- get() for retrieving a single item from an object-store. It takes one parameter: the key of the item to be retrieved. 
- getAll() for returning all items from an object-store.
- put() for updating an already existing data in an object store
- delete() for deleting a single item from an object-store. It takes one parameter: the key of the item to be deleted.

{{< highlight js >}}
// using getAll() verb as an example

taskDatabasePromise.onsuccess = (event) => {
    taskDatabase = event.target.result;
    const tx = taskDatabase.transaction('Tasks', 'readonly')
    const objectStore = tx.objectStore('Tasks')
    const objectStoreRequest = objectStore.getAll();

    objectStoreRequest.onsuccess = (e) => {
        const result = e.target.result;
        // Use the result as needed
    }
}
{{< /highlight >}}

For more details on verbs, visit [here](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore).



