# Javascript: Language of the Web

Javascript is primarily client side language which gets executed on webpages to make it more interactive (in short telling web browser do some dirty work). It is interpreted and not a compiled language but now days modern web browser use a technology knows as JIT (Just In-Time) compilation. Usually when you execute javascript code in browser console then behind the console it is REPL (Read-Eval-Loop Loop), this is what console runs.

Object Oriented Language with number, string, boolean, null, undefined

### An Object

> An Object is a collection of properties and has a single prototype object. The prototype may be either an object or the null value.
```
var foo = {
   x: 1,
   y: 2
};
```
So now we have 2 explicit and 1 implicit properties (__proto__). But why Proto ?<br>
Since ECMAScript has no concept of class so we needed one way to inherit some part because there might be the case where you needed to reuse some part of your code, so we used delegation based inheritance (in terms of ECMAScript it is prototype based inheritance). So for this we used Prototype chain

> A prototype chain is a finite chain of objects which is used to implement inheritance and shared properties

Example:


### Javascript is 2-pass Read

Javascript first parse the code, collects function definitions, hoist variables and at second pass it actually executes the code.<br>
Example:
```
foo();

function foo() {
    console.log('bar');
}
```
This one will give reference error:
```
foo(); // Error: Foo is not defined

foo = function() {
    console.log('bar');
}
```

#### Fact: Functions are First class objects

In short Functions are objects, which means they inherit from the Object prototype and they can be assigned key: value pairs or can be returned from functions. They can be assigned to variables or pass around as arguments. But why it matters ? Because you can do great things like callback functions with event listners, closures are also something which we can use.

### Trust Issues ? Order of Execution



### Understanding Javascript Engine and Javascript Runtime
Because of this nature javascript should do 2 things:<br>
1. [Parsing](https://en.wikipedia.org/wiki/Parsing) and converting your code to runnable commands<br>
2. Using Environment object to interact

So here 2 things are in action. First is Javascript Engine and second is Javascript Runtime. For example V8 engine is used by both Chrome Browser and NodeJS but their runtime can be different as:<br>
1. Chrome which have the window with DOM objects<br>
2. Node with processes, buffers and more

One good question can be raised is:

[How does hoisting work if JavaScript is an interpreted language?](https://stackoverflow.com/questions/45620041/how-does-hoisting-work-if-javascript-is-an-interpreted-language)<br>
Or maybe much better question is:

[Why does JavaScript hoist variables?](https://stackoverflow.com/questions/15005098/why-does-javascript-hoist-variables)

Because that's how Javascript Interpreter works and implemented. It works in 2 passes:

1. Processes variable and function declarations<br>
2. Code is executed & processes function expression and undeclared variables.

### Declarations

In most of the languages variables are created at the spot where the declaration occurs like in C language. In Javascript this is not the case, it actually depends on how you declare them and ECMAScript 6 offers options to make controlling scope easier.

Side videos:

[The Birth & Death of Javascript](https://www.destroyallsoftware.com/talks/the-birth-and-death-of-javascript)







Reference:

[Links]<br>
https://www.quirksmode.org/js/intro.html
https://appendto.com/2016/10/javascript-functions-as-first-class-objects/
http://dmitrysoshnikov.com/ecmascript/javascript-the-core/

[Social Links]<br>
https://www.quora.com/What-is-the-difference-between-javascript-engine-and-javascript-runtime

[PDF Links]<br>
http://sd.blackball.lv/library/JavaScript_Patterns_%282010%29.pdf

### TODO:

1. Read about Parser for better understanding
