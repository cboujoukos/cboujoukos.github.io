---
layout: post
title:      "Javascript Constructors and Prototypes"
date:       2018-05-21 20:28:33 +0000
permalink:  javascript_constructors_and_prototypes
---


### First things first: what is a constructor, and why do we need it? 

Javascript, unlike ruby and many other languages, does not natively support the idea of "classes". A constructor is a function which allows us to take advantage of similiar behavior and functionality that classes offer to numerous other programming languages. A constructor in javascript is simply a function that is called with the `new` keyword to instantiate a new instance of your Object, or "class" if you will. A constructor can be thought of as a blueprint, it is a way to package properties and behaviors to be reused by many similar objects. Let's look at a quick example.

```
function Dog(name,age,breed){
    this.name = name;
		this.age = age;
		this.breed = breed;
}

var fido = new Dog("Fido", 3, "Golden Retriever");

fido #=> Dog {name: "Fido", age: 3, breed: "Golden Retriever"}
```
Simple enough so far. We have our constructor, Dog(), with the parameters of name, age and breed. To construct a new Dog object, we utilize the `new` keyword and call dog, passing in three arguments. This *constructs*  a new Dog. Within the context of the constructor function, the keyword `this` does not have a value. Instead, the value of `this` becomes the newly created object.

### Okay, so then what is a prototype?

When a new object is created, that new object inherits a set of properties from its prototype. In fact, all objects inherit a set of properties, including a `constructor` property, from their prototype.

```
var newString = new String("I am a new string!")

newString.constructor == String;                                             // true

fido.constructor == Dog;                                                              // true
```
When we instantiated fido, in addition to setting fido's properties to the arguments we passed in, javascript also set a special `constructor` property on fido, which points to the Dog constructor!

Let's take it one step further.

We have already established that each new instance of a Dog object inherits properties from its prototype, Dog. Keeping this in mind it is easy enough to create reusable functions that you want to be accessible to every dog object.

```
Dog.prototype.speak = function() {
    return "Arf, arf!"
}

fido.speak()                                                                                      // "Arf, arf!"
```

As you can see, by tailoring the prototype, we can modify the behavior of every instance of dog even after it has been constructed. We could even add additional properties to our prototype, and then watch in amazement as we see that our dog instances have inherited said properties!

```
Dog.prototype.legCount = 4;

fido.legCount                                                                                // 4

fido                                                                                                    
// Dog {name: "Fido", age: 3, breed: "Golden Retriever", legCount: 4}
```

How neat is that!?
