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
#### Open Source
* V8 is the open source Javascript engine used by Google Chrome.
* The engine itself is written in C++.
* It compiles your Javascript programs into which ever machine code language your processor understands.
* Since its a C++ program, if you wanted, you could embed V8 in your own C++ programs. 
* Source: https://github.com/v8/v8
#### V8 Hooks
* Javascript was developed for the browser, but in V8 you can add features that are only available to C++
* Basically you would create a C++ function than can be told to run by a Javascript command.
* This is what allows you to access files and folders, connect to a database, and establish a server.
#### Browsers
* Chrome is a program built on V8 with additional features that make it suitable as a browser.
* Some additional functions include accessing the DOM and making HTTP requests.
* These features are not part of the ECMAScript Spec, they are added with C++ functions.
#### NodeJS
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
Come to the meetup =]