---
layout: post
title:      "JavaScript Scope, Hoisting, and Arrow Functions"
date:       2018-06-12 18:35:28 +0000
permalink:  javascript_scope_hoisting_and_arrow_functions
---


Every JS developer has heard the terms scope and hoisting thrown around, but many, like myself, may not have had a concise understanding of what they meant. Well, let's remedy that.

## Scope

Scope refers to the accessibility of your variables, functions, and objects during runtime of different parts of your code. Generally speaking, these elements have either global scope or local scope (although we will also touch on block scope a little later).  A variable's scope is determined by the location in your code in which the variable was declared. Variables declared outside of a function have global scope, whereas variables declared within a function have local, or function scope. Beware, if you assign a value to a variable that has not been declared(with var, let or const) it will automatically be assigned global scope, even if the assignment occured within a function! Global variables exist for the entire runtime of your application, whereas a local variable exists only until the function in which it was declared has completed. When JS resolves a variable, it begins by searching for a declaration of that variable in the current scope, or the innermost function, and then works its way outwards until it reaches the top-level, or the global scope. In this way, a function has access to variables declared in its function scope, in any enclosing functions, and the global scope. This is referred to as the scope chain. 

## Hoisting

The term hoisting refers to way that JavaScript “hoists” functions declarations and variables declared with `var` to top of their scope before executing any code. The terminology is really just a way for us to envision the way that our code is run, the JavaScript engine doesn’t actually shuffle our code around, moving functions and variables up to the top. Under the hood, all that’s happening is the JavaScript engine is making multiple passes through the code, and on the first pass it is storing our function declarations and variables in memory before it executes any code. It is important to note, however, that for variables the interpreter only ‘hoists’ the variable *declaration*, not the actual assignment of a value to the variable. So essentially, the JavaScript engine will interpret:
```
console.log(greeting);
hello();
function hello(){
    console.log(‘hello’)
};
var greeting = “Hi there”;
console.log(greeting)
```
As:


```
function hello(){
    console.log(‘hello’)
}
var greeting;

console.log(greeting)hello();
greeting = “Hi there”;

console.log(greeting)

 #=> undefined
 #=> hello
 #=> “Hi there”
```
This seems like a good place to point out that undefined is NOT to be confused with *not defined* or *undeclared*. When the code above is run, the first time we console.log(greeting), the result is undefined, which means that the program knows the variable exists (thanks to var hoisting), but that it has not yet been assigned a value. If we had thrown a console.log(hamSandwich) in there, instead of logging ‘undefined’ we would have thrown a ReferenceError: hamSandwich is not defined. So even though when the program executes the variable ‘greeting’ has not yet been declared, the interpreter already knows that the variable exists at runtime.

If you, dear reader, are fairly familiar with ES6, you have probably noticed a couple of hints of what is to come next. I mentioned that specifically variables declared with `var` are hoisted, as well as briefly touching on block scoping. This brings us to our next topic, `var` versus `let/const`. 
Up until ES6, all variables in JavaScript were declared with the keyword `var`. ES6 introduced two new keywords for declaring variables, `let` and `const`. What was wrong with `var`, you ask? Well, for starters, one major drawback with `var` is that JavaScript won’t throw an error if you declare the same variable twice. This has the potential to cause unexpected results, and can make debugging an issue more challenging. Both `let` and `const` on the other hand, will clearly complain if you try to declare a variable twice with the same name in the same scope. Another pitfall of `var` is that it lacks block scope. Block scope is similar to function scope, except it encompasses anything within a set of curly brackets. This applies not just to functions, but also to if statements, for loops, and the like. Let’s take a look at a couple of examples.
```
for (var i=0; i<10; i++) {
  // block scope for the for statement
}
console.log(i) // => 10
```
Versus
```
for (let i=0; i<10; i++) {
  // block scope for the for statement
}
console.log(i) // => ReferenceError: i is not defined
```
With `let`, i is scoped to the block in which it was defined, and is ONLY available within that scope. More specific scope means more predictable code!
The keyword `const`, similarly is block-scoped. The major difference between `let` and `const` is that variables declared with const cannot be reassigned. This is not to be confused with immutability. If the variable declared with `const` points to a mutable element such as an array or an object, even though the variable cannot be *reassigned*, its contents can still be altered.

## Arrow Functions

Arrow functions are yet another new feature to ES6. Arrow functions are essentially just a more concise way to write function expressions in JavaScript. The basic syntax is:
```
(param1, param2, …, paramN) => { statements } 
(param1, param2, …, paramN) => expression
```
If there is only one parameter the parentheses can be omitted, and in the second example, when the curly brackets are omitted the expression is the *implicit* return value.
Here is a quick example.

```
///// ES5

var add = function(a,b){
    return a + b
}
add(2,3)
#=> 5

////// ES6
const add = (a,b) => a + b;
add(2,3)
#=> 5
```
Right off the bat, you’ll notice that the same function went from taking up 3 lines of code in the ES5 example to only 1 line of code with our new arrow function. Concise, sleek, to the point. The other major difference between arrow functions and regular function expressions is the way in which `this` binds in functions. Prior to the advent of arrow functions, every function in JavaScript defined its own value of `this`. An arrow function on the other hand, gets its value of this from the enclosing execution context.

```
function Dog() {
    this.age = 0;
    setInterval(function growUp() {
        this.age++;
    }, 1000);
}
var lucy = new Dog()

lucy.age
#=> 0
lucy.age
#=> 0

function Cat() {
    this.age = 0;
    setInterval(() => {
        this.age++;
    }, 1000);
}

var rosa = new Cat()

rosa.age
#=> 3

rosa.age 
#=> 7

```
In the first example, the growUp() function defines `this` as the global object, NOT the `this` defined by the Dog object. In the second example, on the other hand, `this` correctly refers to the same `this` as the enclosing function Cat(), thanks to our handy arrow function.

