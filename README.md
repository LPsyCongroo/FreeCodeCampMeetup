# Understanding NodeJS with Tony Alicea

## Section 1: Course Introduction

* Commandline basics (mkdir, cd, ls, touch, etc)
* Setup


## Section 2: V8 Engine

### 2.1 Layers of Abstraction in Computing
#### Microprocessors
* Gets instructions in machine code languages like IA-32, x86-64, ARM, Microprocessors
* Though we don't often program in machine code anymore, the programs we write are compiled down machine code.
* Modern Programming languages are built on top of many layers of abstraction.
#### Assembly
* Easier to read than machine code but still low level of abstraction
#### C/C++
* Higher level of abstraction but still gives you a degree of control over lower level processes
* Hence, very widely used.
#### Javascript
* Much farther abstracted than the rest.

### 2.2 ECMAScript
There are many Javascript engines, all are based on the ECMAScript specification standard https://www.ecma-international.org/ecma-262/6.0/

### 2.3 V8 ENGINE
##### Open Source
* V8 is the open source Javascript engine used by Google Chrome.
* The engine itself is written in C++.
* It compiles your Javascript programs into which ever machine code language your processor understands.
* Since its a C++ program, if you wanted, you could embed V8 in your own C++ programs. 
* Source: https://github.com/v8/v8
##### V8 Hooks
* Javascript was developed for the browser, but in V8 you can add features that are only available to C++
* Basically you would create a C++ function than can be told to run by a Javascript command.
* This is what allows you to access files and folders, connect to a database, and establish a server.
##### Browsers
* Chrome is a program built on V8 with additional features that make it suitable as a browser.
* Some additional functions include accessing the DOM and making HTTP requests.
* These features are not part of the ECMAScript Spec, they are added with C++ functions.
##### NodeJS
* NodeJS is a program built on top of V8 with added features that make it suitable as a server-side technology.
* The goal with NodeJS is to use Javascript to make server applications that previously required another language.


## Section 3: NodeJS Core

### 3.1 What does Javascript need to manage a server?
* Better ways to organize code into reusable pieces?
        (NodeJS came before ES6 came up with an answer to this question.)
* Ways to deal with files
* Ways to deal with Databases
* Ability to communicate over the internet
* Ability accept requests and send responses
* Ways to deal with work that takes a long time
### 3.2 C++ Core
* In the node source code "deps" (dependencies) folder are the major parts of NodeJS
  * The familiar ones may include the "v8" folder and the "npm" folder
* In the "src" folder is the C++ core: the utilities written in C++
### 3.3 Javascript Core
* The "lib" folder contains pure Javascript written to perform tasks available to Node
* While some are just useful functions, many of these are actually wrappers for C++ code.


## Section 4: Modules
### 4.1 Introduction
Having a module system allows us to separate out and encapsulate our Javascript code. Although modules are supported by ES6, they did not exist for Javascript when NodeJS was first built, thus the creators of NodeJS made their own using the CommonJS specification. By having functionality delegated to different modules, we can prevent unwanted interference between them because all the variables and functions are in seperate scopes. However, by using the export syntax, we can choose to access certain parts of the code in other modules.

### 4.2 Syntax
Let's build our first module! The syntax is quite simple; whatever needs to be made available is set equal to `module.exports`.
```javascript
// first-module.js
var one = "export me!";
var two = "leave me alone.";
console.log("var one says " + one + " but var two says " + two);
module.exports = one;
```
Then in our other file we'll pull in our module by passing the URI to the `require` function. 
```javascript
// app.js
var importedOne = require('./first-module.js');
console.log("our imported value is " + importedOne);
```
When we execute app.js the resulting console should read:
```
var one says export me! but var two says leave me alone.
our imported value is export me!
```
Some things to notice:
* Notice that the first item logged to the console is from the module file. When the `require` function is first called it parses and evaluates the code in the module and then returns `module.exports`.
* The `'./'` in the URI passed to `require()` means that the module we are importing is in the same directory as the one we are importing it to. A `'../'` would have told Node to go up a directory. If you've installed a Node module using NPM or you want to use one of Node's own native modules, you can import it by writing the file path without any backslashes or dots and Node will know where to look for it.
* If you're importing a Javascript file you can leave out the extension as Node will add it by default: `require('./first-module')`. On the other hand, if you're importing another file type like a JSON, you will need to specify the extension.
* If you leave out the extension and no file with that name is found, `require()` will look for a folder with that name and import the file `index.js` by default if it has one.
* `require()` and `module` are objects that Node makes available to your code's execution context when it runs it. Similar to how when you run Javascript in the browser you have access to the `window` object. We'll cover how Node does this in just a bit.

### 4.3 `require()` and `module` under the hood
When we run Javascript code through NodeJS, what we write is not the only thing that gets parsed. NodeJS actually wraps the code in an Immediately Invoked Function Expression (IIFE).
```javascript
(function (exports, require, module, __filename, __dirname) {
    //CODE
})(module.exports, require, module. filename, dirname);
```
This serves several purposes, one of which is to encapsulate the code in the IIFE's execution context, preventing interference with other parts of the code base. The `module` object is passed into the IIFE as a parameter and since objects are passed by reference, whatever is set equal to `module.exports` is made available to the outside.

The function `require` is a globally available object that directly returns the `module.exports` associated with the file path passed as a parameter. Under the hood, `require` also caches the result so that subsequent calls to import the same module return the same object in memory. We'll see why this is useful later on.

For a deeper look at `require` under the hood as well as a comparison with the ES6 module system (and if you want to learn about the woes of maintaining big open source projects like NodeJS), check out this article:
https://hackernoon.com/node-js-tc-39-and-modules-a1118aecf95e
and the follow up:
https://medium.com/the-node-js-collection/an-update-on-es6-modules-in-node-js-42c958b890c

Try diving right into the source code! Using Visual Studio Code's Node Debugger, set a breakpoint next to the require function. Then click the down arrow to step-in so you can see what happens internally for yourself!

### 4.4 Module Patterns
Let's take a look at some common design patterns used with modules.
#### Pattern 1
The first is quite straight-forward: set `module.exports` to a function.
```javascript
// greet1.js
module.exports = function() {
	console.log('Hello world');
};
```
```javascript
// app.js
var greet = require('./greet1');
greet();  //=>"Hello World"
```
#### Pattern 2
The next pattern makes of use of the fact that `module.exports` is set to an empty object by default. We can just directly add properties to it.
```javascript
// greet2.js
module.exports.greet = function() {
	console.log('Hello world');
};
```
```javascript
// app.js
var greet2 = require('./greet1').greet;
greet2();  //=>"Hello World"
```
##### Side Note:
You may have noticed something odd in the previous section about the IIFE wrapper around the module code:
```javascript
(function (exports, require, module, __filename, __dirname) {
    //CODE
})(module.exports, require, module. filename, dirname);
```
NodeJS passes the `module` object as a variable called `module` which allows us to call `module.exports`. But it also passes in `module.exports` as the variable exports. So why even pass `module` directly, after all isn't `exports` more concise?

Well it is true but there is a problem. The `require` function returns `module.exports` directly. Remember that in Javascript, objects pass by reference. That means that if we ever change what the variable `exports` points to it won't get imported by `require`!
```javascript
exports = "hello world!";
// Require will return the empty default object!
```
Since we can still modify the properties in an object, Pattern 2 above is the only way we can really use the `exports` variable:
```javascript
exports.greeting = "hello world!";
// Require will return { greeting: "hello world!" }
```
Try this out in Node and see for yourself.

#### Pattern 3
This next pattern exports a new instance of an object. However, because `require` caches the results of previous imports, something interesting happens when we call `require` a second time.
```javascript
// greet3.js
function Greetr() {
	this.greeting = 'Hello world!!';
	this.greet = function() {
		console.log(this.greeting);
	}
}
module.exports = new Greetr();
```
```javascript
// app.js
var greet3 = require('./greet3');
greet3.greet();  //=>"Hello World"
greet3.greeting = 'Changed hello world!';
greet3.greet();  //=>"Changed hello world!"

var greet3b = require('./greet3');
greet3b.greet();  //=>"Changed hello world!"
```
You may have expected a new instance of a Greetr type object to be created when `require` was called a second time. But instead we got the same exact object in memory! In fact, when `require` is called for the same module, not only does it return what you set to `module.exports` from the cache, it actually doesn't even evaluate the code from the module at all. Try experimenting with some console.logs:
```javascript
// mod.js
var thing = 'hello world';
console.log('this is executed from the module - but only once!');
module.exports = thing;
```
```javascript
var myMod = require('./mod');
console.log('the result from the first require is', myMod);
var myMod2 = require('./mod');
console.log('the result from the second require is', myMod2);
```
Console result:
```
this is executed from the module - but only once!
the result from the first require is hello world
the result from the second require is hello world
```
You might say: "Hey, didn't you say earlier that caching was useful?" It is! Caching modules allows us to preserve state for our modules and this can be a powerful tool. It also optimizes performance.

You might press on: "But I still want a new instance of the module everytime I import it!" No worries friend, the next pattern solves this problem.
#### Pattern 4
This next pattern solves our problem from before by exporting the constructor itself instead of an object instance. Now you can make all the separate instances you need! You can also use ES6 classes.
```javascript
// greet4.js
function Greetr() {
	this.greeting = 'Hello world!!!';
	this.greet = function() {
		console.log(this.greeting);
	}
}
module.exports = Greetr;
```
```javascript
// app.js
var Greet4 = require('./greet4');
var grtr = new Greet4();
grtr.greet();  //=>"Hello World"
```
#### Pattern 5: Revealing Module Pattern
This last pattern is so common it even has a name for it. The Revealing Module Pattern is a clean way to organize your code by exposing only the properties and methods you want.
```javascript
// greet5.js
var greeting = 'Hello world!!!!';
function greet() {
	console.log(greeting);
}
module.exports = {
	greet: greet
}
```
```javascript
// app.js
var greet5 = require('./greet5').greet;
greet5();  //=>"Hello World"
```
Thanks to closures, the `greet` function still has access to the `greeting` variable but the module that is importing it does not!

## Section 5: The Event Emitter
### 5.1 Introduction
Event: Something that happened that our app responds to.
In NodeJS there are actually two kinds of events: 

System Events come from the C++ core of NodeJS, located in the libuv folder. It deals with events related to the computer system (received files, data from the internet, etc).

Custom events are completely different; they are part of the Javascript core and can be used to make custom events in Javascript using what is called the Event Emitter.

These two event systems are often related, as much of Node's C++ functionality is wrapped in Javascript code. When a computer system related event occurs, there is often a Javascript event associated with it.

The ECMAscript specification does not actually have a concept of events, so instead Node "fakes" it by building its own Event Emitter. It's not as difficult as it sounds—in fact, let's make one right now.
### 5.2 Let's Make an Event Emitter
We'll start by creating a function constructor for our Event Emitter. For now it will take an object as a property.
```javacript
// emitter.js
function Emitter() {
    this.events = {};
}
```
Next, we'll add some methods to the prototype. The first is the `on` method. It will take two parameters: `type` which will be the type of event and `listener` which will be the event listener. (An event listener is a function that is called in response to an event. If you've worked on front-end Javascript, you are likely familiar with the `addEventListener` function. Our `on` method is not exactly the same, but it performs a similar task!) We then check to see if there is already an event of type `type`. If there isn't, we'll set it as a new array. Then we add the listener to the array.
```javascript
Emitter.prototype.on = function(type, listener) {
    this.events[type] = this.events[type] || [];
    this.events[type].push(listener);
}
```
The second method is called `emit`. This will just take an event type as a parameter. Everytime this method is called, it will see if there exists an event of the kind `type`. If there is, it will loop through the array of all the listeners that were added and call each one. When our app needs to say that an event was called, it uses the `emit` method to call all the responses to that event.
```javascript
Emitter.prototype.emit = function(type) {
    if(this.events[type]){
        this.events[type].forEach(function(listener){
            listener();
        });
    }
}
```
Now let's apply our new event emitter.
```javascript
// app.js
var Emitter = require('./emitter');
var emtr = new Emitter();

emtr.on('order66', function(){
    console.log('Execute Order 66.');
});
emtr.on('epic event', function(){
    console.log('PEW PEW PEW');
});
emtr.on('epic event', function(){
    console.log('Wilhelm scream);
});
emtr.emit('order66');
```
The resulting console will read:
```
Execute Order 66
PEW PEW PEW
Wilhelm scream
```
Although the native NodeJS event emitter is a bit more complex, this is essentially how it works. I bet by now you're blown away by how simple it is. 

![alt text](https://media1.tenor.com/images/d00d4c0c61062ff1fa2ce56263430c04/tenor.gif?itemid=8019692 "Ohmehgerd Palpatine soooo amazed!")

### 5.3 The Node Event Emitter
