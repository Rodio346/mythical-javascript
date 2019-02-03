# Javascript: Language of the Web

Javascript is primarily client side language which gets executed on webpages to make it more interactive (in short telling web browser do some dirty work). It is interpreted and not a compiled language but now days modern web browser use a technology knows as JIT (Just In-Time) compilation. Usually when you execute javascript code in browser console then behind the console it is REPL (Read-Eval-Loop Loop), this is what console runs.

ECMAScript is Object Oriented Language with the prototype-based organization, having the concept of an object as its core abstraction

Javascript code is actually get executed by Javascript Engine which is a program or an interpreter. In earlier day it acts like a standard interpreter but nowdays most of them uses JIT (Just in Time) compiler where the javascript code compiles Javascript code to bytecode. Example: V8, Rhino, SpiderMoney, JavaScriptCore, KJS, Chakra, Nashorn, JerryScript. Now engine is obviously not written in javascript but in case of V8 it is written in C++. In case of V8 the Javascript execution is different. It first compiles Javascript code into machine code by implemented JIT compiler (same as Rhino and SpiderMoney does) but it does not create byte code or intermediate code.

Abstract Syntax Tree is also one of the main goal of V8 when doing parsing.

> Better exlanation here: http://thibaultlaurens.github.io/javascript/2013/04/29/how-the-v8-engine-works/

At this level engine (V8 in this example) runs multiple thread for optimization. Main thread will obviously do the tradional work: Fetch, Compile, Execute but other threads like Profiler thread tell runtime on which methods it will spend a lot of time and so that different compiler can optimize it (In V8 2 different compilers used for this reason). After optimization compilation to machine code takes place.

```
This parts need more info and I am lost here
```

#### Javascript runtime

Now besides engine there are other things working too like Web APIs which we use and other supports like Concurrency and the even loop.

1. The Call Stack

one thread == one call stack == one thing at a time

But what if some process happens which takes time or blocking it. To avoid this we have webapi's available by browser. At the same time we have event loop and task queue. `TODO: Not sure where event loop and task queue reside`. Event loop push only when stack is empty. AJAX request which we made also reside in webapis rather than the javascript itself. Also let's say we have on click event then that is webapis event and on click it will push defined process in the queue and that's how it gets pushed in stack and get executed.

> Don't block the event loop

There is another thing called render queue, browser repaints it to re-render it. But with your javascript code you might make it block by writing code and blocking event loop. So it's better to queue up those events.

### An Object

> Ref to the Legendary blog:
> http://dmitrysoshnikov.com/ecmascript/javascript-the-core
> http://dmitrysoshnikov.com/ecmascript/javascript-the-core-2nd-edition/

> An Object is a collection of properties and has a single prototype object. The prototype may be either an object or the null value.
```
var foo = {
   x: 1,
   y: 2
};
```

Generally there are 2 main types of objects:

1. Native - Described in the ECMAScript standard
   a. Built-in: Array/Date etc
   b. User-defined: `var a = {};`
2. Host - Defined by the host environment (ex. browser environment) like window and DOM objects.

Now when we create any new object then it is not entierly empty but have some built in properties inherited.

So now we have 2 explicit and 1 implicit properties (__proto__). But why Proto ?<br>
Since ECMAScript has no concept of class so we needed one way to inherit some part because there might be the case where you needed to reuse some part of your code, so we used delegation based inheritance (in terms of ECMAScript it is prototype based inheritance). So for this we used Prototype chain

> Prototype: A prototype is a delegation object used to implement prototype-based inheritance.
> A prototype chain is a finite chain of objects which is used to implement inheritance and shared properties

Example:
```
var a = {
   x: 10,
   calculate: function (z) {
       return this.x + this.y + z
   }
};

var b = {
   y: 20,
   __proto__: a
};

var c = {
   y: 30,
   __proto__: a
};
// Now we can call by using b.calculate(30); etc
```
Now the question arises is, How to know which method to invoke ?<br>
So if a property or method is not found in the object itself then there is an attempt to find this property/method in the prototype chain. If then also not found then this step continues. So the first method/property which is found is used first. If nothing is found then `undefined` value is returned.

Same concept goes for `this` keyword. Like in given example if we invoke calculate function from `b` object then `this.y` in calculate refers to the original object which is `b` and then in case if it is not found then it will check `__proto__` and it goes on, this mechanism is known as dynamic dispatch or delegation. Also if prototype is not specified explicitly then default value for `__proto__` is taken which is `Object.prototype` which is final link of a chain and it's own `__proto__` value is set to `null`.

> Delegation: a mechanism used to resolve a property in the inheritance chain. The process happens at runtime, hence is also called dynamic dispatch.

Generally it is preferred to use API methods for prototype manipulation such as `Object.create()`, `Object.setPrototypeOf(,)` and more.

Now according to Stoyan Stefanov in book Javascript Patterns:

> In software development, a pattern is a solution to a common problem. A pattern is not necessarily a code solution ready for copy-and-paste but more of a best practice, a useful abstraction, and a template for solving categories of problems.

Same goes for Javascript, if there are patterns which might have same set of properties then we use constructor function which helps to generate objects by specified pattern.<br>
Contructor in short does:

1. Creation of objects by specified pattern
2. Automatically sets a prototype object for newly created objects (Found in ConstructorFunction.prototype property).

<b>Example</b><br>
```
// Constructor function which have specified patterns common to multiple objects
function Foo(y) {
    this.y = y;
};

// Foo.prototype stores reference to the prototype objects of newly created objects
// which means it is inherited and shared properties or methods
Foo.prototype.x = 10; // It means we want x value to be shared to all inheried object

Foo.prototype.calculate = function(z) {
    return this.x + this.y + z;
};

// Using pattern Foo in our object b and c
var b = new Foo(20);
var c = new Foo(30);

// Now we can use inherited properties as
b.calculate(30); // 60
b.calculate(40); // 80

// For better understanding
b.__proto__ === Foo.prototype
c.__proto__ === Foo.prototype

```

Also `Foo.prototype` automatically creates a special property called `constructor` which is a reference to the constructor function itself. So if we talk about `Foo` object then it consist of:

1. Some properties
2. `__proto__` which is our `Function.prototype` object
3. `prototype` which is our `Foo.prototype` object. Inside `Foo.prototype` we have `constructor` (Foo), some properties and `__proto__` which will be `Object.prototype`.

So now the combination of the constructor function and the prototype object may be called as a "class". Also Python first class dynamic classes have the same implementation of properties/methods resolution. So Python are just a syntactic sugar for delegation based inheritance used in ECMAScript. Now in ES6 the concept of "class" is standardized and is implemented as a syntactic sugar on top of the constructor functions as we learned.

### Class

Being a syntactic sugar it does work exactly like how we discussed in our previous example with prototype objects. Example:

```
class Letter {
    constructor(number) {
        this.number = number;
    };

    getNumber() {
        return this.number;
    }
}
```

Here class based inheritance is implemented on top of the prototype based delegation. Also "class" is represented as a "constructor function + prototype" pair. In short it creates object and also automatically sets the prototype for its newly created instance.

> Constructor: A constructor is a function which is used to create instances, and automatically set their prototype.

### Execution Context Stack

Every code is evaluated in its execution context and by EC we talk about the properites/methods in which Javascript code has access. In simpler words it is the internal javascript construct to track execution of a function or the global code which is defined by the ECMAScript spec. At the same time we refer it as an environment in which javascript code is executed. Generally there are 3 types of ECMAScript code used:

Keywords: Control flow and order of execution

1. Global code - There is always 1 global context
2. Function Code - For every call of a function there is new function execution context
3. Eval code - For each call of `eval` function there is new `eval` execution context

Later in ES2015+ there is another code type introduced which is:<br>
4. Module code

Now execution context may activate another context like function calling another function and this is handled by execution context stack.<br>
Context which activate another context is called `caller` and the one which is being called is `callee`. Whenever a caller activates a callee then the caller suspends its execution and passes the control flow to the callee. Now Callee is pushed onto the stack and become running execution context. Once it ends, it returns control to the caller.

In ECMAScript, the program runtime is presented as the execution context (EC) stack where top of the stack is an active context.<br>

Now one good question which can be asked is why stack is used in Javascript ? Well thats because Javascript is a <b>single threaded environment</b> and only one code is execute at a time.

Personal note: ES6 feature update i guess:<br>
Every execution context has an associated lexical environment.

> Lexical environment: A lexical environment is a structure used to define association between identifiers appearing in the context with their values. Each environment can have a reference to an optional parent environment.

In short environment is a storage of variables, functions and classes defined in a scope.

Now during ES3, An execution context can be represented as a simple object with properties which is called as context state to track the execution progress.

#### 1. Variable Object: vars, function declarations (no expressions), arguments<br>
> A variable object is a container of data associated with the execution context. It’s a special object that stores variables and function declarations defined in the context.

<b>Example</b><br>
```
var foo = 10;

function bar() {} // Function Declaration
(function baz() {}); // Function Expression

console.log(this.foo == foo) // true
console.log(window.bar == bar) // true
console.log(baz); // Not defined
```

So here Global context VO will have `foo` and `bar` with some built ins.<br>
<b>Note: In global context the variable object is the global object itself, that's why we have an ability to refer global variables via property names of the global object</b><br>
Example:<br>
`window.bar` in above example

Also in ECMAScript only functions create a new scope and variable with inner functions defined within a scope of function do not pollute the global variable object.<br>
So we talked about global context, in terms of eval context global variable object or caller variable object is used but in case of function context, a variable object is presented as an activation object.

#### 1.1 Activation Object

When a function is called, a special object is created which is filled with formal parameters, special arguments (special because it is a map of formal parameters with index properties) which is used as variable object of the function context.

Example:
```
function foo(x, y) {
    var z = 30;
    function bar() {} // FD
    (function baz() {}); // FE
}

foo(10, 20);
```

So our AO of the `foo` function will have:

1. Formal parameters: `x` with value 10 and `y` with value 20
2. Special arguments: arguments with value `{0: 10, 1: 20, ...}` with index properties
3. Declarations: variable `z` with value 30 and function bar as reference.

#### 2. Scope Chain: variable objects and all parent scopes

> A scope chain is a list of objects that are searched for identifiers appear in the code of the context.

It is important because of nested functions usage. It is similar to prototype chain where if variable is not found in current scope (in it's own variable/activation object) then the lookup proceeds to its parent variable/activation object and repeats itself until it finds it else it is `undefined`. In contexts in function refers in its code the identifier (name of var, FD, formal parameters) which is not local then it is called a free variable and to search these free variables exactly a scope chain is used.

Example:
```
var x = 10;
(function foo() {
  var y = 20;
  (function bar() {
    var z = 30;
    // x and y are free variables
    console.log(x + y + z);
  })();
})();
```

So here scope chain objects are handled via the implicit `__parent__` property which is just a reference to the other activation object. For example:<br>
`bar` AO will have declarations with `__parent__` pointing to `foo` AO and that will be pointing to global VO and that again will be pointing to null.

Now since they all are object, they may also have prototypes and prototype chains which means scope chain lookup is 2 dimensional.

#### Note: Global variable object usually inherit from `Object.prototype` object hence Global VO `__proto__` refer to the `Object.prototype` (In case of SpiderMonkey but for different case it might inherit from something else).

Right now we understood that object can inherit values from another object and at the same time have scope. So the question arises is which will be looked first when asked. Whenever any variable is invoked then it is looked at `__proto__` first and then `__parent__`. So if we write something like:
```
  with ({z: 50}) {
    console.log(x, z);
  }
```

Then for x, it will see if there is anything defined in `with` object `__proto__` property, means it will see if anything is defined in `Object.prototype` object and if nothing is found then it will do scope chain and find in another activation object and it goes on.

Also when any context ends then all the state and itself gets destroyed. But we know that functions are first-class objects, means it can be returned and can be activated from another context. But what if these function access some free variable which is already gone ? So to solve this complexity we have closure.

<b>Fact: </b>Higher-order function are those function which takes functions as arguments and they are closer to mathematics operators and functions which return other functions are called function valued functions.

#### Closures | Solving 2 Funarg problem

Concept related with scope chain

First problem is "Upward funarg problem"<br>
When a function is returned and if it is using free variable then it should be able to access it even after when parent context is ended.<br>
To solve that the inner function saved parent scope chain in its `[[Scope]]` property and when function is activated, the scope chain of its context is formed as combination of the activation object and this `[[Scope]]` property. So exactly at creation moments the parent context is saved in inner function.<br>
Example:

```
function foo() {
    var x = 10;
    return function bar() {
        console.log(x);
    };
}

var returnedFunction = foo();
var x = 20;
returnedFunction(); // Output 10 but not 20
```

This scope is static scope or lexical scope because variable was found in `[[Scope]]` first.<br>
Second problem is "Downward funarg problem"<br>
> Parent context may exist but may be an ambiguity with resolving an identifier. In short which scope of value should be used, statically saved or dynamically created.

In such case we use static scope. Example:

```
var x = 10;

function foo() {
    console.log(x);
}

(function (funArg) {
 var x = 20;
 funArg();
 })(foo);
```

Now this will print 10 because in `foo` function global value x was statically saved in `[[Scope]]` property. So we conclude that:

> Static scope is an obligatory requirement to have closures in a language

Definition:

> A closure is a combination of a code block (in ECMAScript this is a function) and statically/lexically saved all parent scopes. Thus, via these saved scopes a function may easily refer free variables.

In ECMAScript all functions are closuers because every function saved `[[Scope]]` property at creation.<br>
<b>*** Note: </b>It is possible that 2 inner/global function share same parent scope, so change by any function can reflect changes in another.

Because of above note creating function in loop result in same result. Example:

```
var data = []
for (var k = 0; k < 3; k++) {
    data[k] = function() {
        console.log(k);
    };
}

data[0](); // 3
data[1](); // 3
data[2](); // 3
```

To avoid this problem one can make use of another object to avoid sharing of scope. Example:

```
var data = []
for (var k = 0; k < 3; k++) {
    data [k] = (function (x) {
            return function() {
              console.log(x);
            };
        })(k);
}
```

But now in ES6 if we simply write `let data = []` with `let k = 0` then this introduced block-scope bindings and easily give correct output

#### 3. thisValue: Context object

> A this value is a special object which is related with the execution context. Therefore, it may be named as a context object (i.e. an object in which context the execution context is activated).

It is the property of execution context (not variable object) but in ES6 it became a property of lexical environment means property of variable object to support arrow function. In global context `this` value is the global object itself, that means `this` value here equals to variable object itself (x, this.x, window.x).

In case of function context it is different it depends on the caller.

### Conclusion

It is hard to handle so many information and at the same time one question might rise which is:

> What's the difference in call stack and execution context stack

For now I believe that they are same (TODO: In doubt). So we know that Global scope is what which is created first internally by Javascript and it is our first execution context. Each execution context will have 4 main things as of latest ECMAScript:

1. thisValue binding
2. Variable Environments
3. Lexical Environments
4. Declaration binding

Stacking occurs whenever you call a function or eval and even a recursive function created execution context.

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
https://www.quirksmode.org/js/intro.html<br>
https://appendto.com/2016/10/javascript-functions-as-first-class-objects/<br>
http://dmitrysoshnikov.com/ecmascript/javascript-the-core/<br>
http://dmitrysoshnikov.com/ecmascript/javascript-the-core-2nd-edition/<br>
https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e<br>
https://medium.com/@js_tut/execution-context-the-call-stack-d1fbe34f6fe9<br>
https://hackernoon.com/execution-context-in-javascript-319dd72e8e2c<br>
https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf<br>
http://thibaultlaurens.github.io/javascript/2013/04/29/how-the-v8-engine-works/<br>
https://medium.freecodecamp.org/whats-the-difference-between-javascript-and-ecmascript-cba48c73a2b5

https://www.youtube.com/watch?v=8aGhZQkoFbQ<br>
(Thanks to https://stackoverflow.com/questions/54503435/whats-the-order-of-execution-of-javascript-code-internally#comment95810892_54503435)

[Social Links]<br>
https://www.quora.com/What-is-the-difference-between-javascript-engine-and-javascript-runtime

[PDF Links]<br>
http://sd.blackball.lv/library/JavaScript_Patterns_%282010%29.pdf

### TODO:

1. Read about Parser for better understanding
2. Create VO/AO diagram for example of for loop in javascript
