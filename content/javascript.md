---
layout: default
title:  "JavaScript"
---

# JavaScript
{:.no_toc}

* TOC
{:toc}

## Overview
This page is a draft and is a work in progress...

## Data Types
### Strings
A String must be surrounded by quotes. There are 3 types of quotes:

1. Double quotes: `"Hello World!"`
1. Single quotes: `'Hello World!'`
1. Backtics: See examples below

Backtics come with the following over single and double quotes. 

```javascript
const newLineSupport = 
  `Backticks support new lines.
  Like this..`;

const myName = 'Koray';
const variableSupport = `Hello ${myName}`!`;
```

### `null` and `undefined`
- `null` represents that some value is missing intentionally.  
- `undefined` represents that a value is missing unintentionally.

`null` is __not__ a _reference to a non-existing object_ or a _null pointer_. The code below represents that name is unknown for whatever reason:

```javascript
let myName = null;
```

`undefined` means _a value is not assigned_. If a variable is declared but not assigned a value, it is `undefined`.

```javascript
let myName;  // undefined
```

You should never explicitly set a variable to be `undefined`. The literal `undefined` value is provided mainly for comparison, to help formalize the difference between an empty object pointer, `null` and an uninitialized variable.

### Cheat Sheet
- You can get a type of a variable using either of:

```javascript
let typeOfVar;

// both return strings
typeOfVar = typeof var  // using as an operator
typeOfVar = typeof(var) // as a function
```

- Result of `typeof null` is `"object"`. This is wrong, `null` is not an object, it is a special value with its special type. The error is kept in the language due to compatibility and was never fixed.
- Result of `typeof alert` returns `"function"` however this is not strictly true. Although `alert` is a function, it is still of type `"object"`. `typeof` treats functions differently for convenience.

## Objects
An object can be created using one of the two syntaxes:

```javascript
let foo = {};
let bar = new Object();
```

Object are useful when they store properties:

```javascript
let employee = {
    firstname: "Koray",
    lastname: "Tugay"
};
```

We can also delete properties from objects:

```javascript
delete employee.firstname; // Removes the property from the object
```

### Functions Returning Objects
Many times there will be functions that create objects to avoid code repetition.

```javascript
function newUser(name, age) {
    return {
        name: name,
        age: age
    };
}
```

And there is a shorthand for this, in case the variables have same names with function properties:

```javascript
function newUser(name, age) {
    return {
        name,  // same with name: name
        age    // same with age: age
    };
}
```

### Flashy Property Names
Objects can have flashy property names within quotes:

```javascript
let foo = {
    "flashy property name": "flash property value"
};
````

### Square Brackets
Flashy property names cannot be accessed using the dot notation, and must use square brackets:

```javascript
let val = foo["flashy property name"];
```

Square brackets also lets referencing property names that are not literals:

```javascript
let preferedName = "firstname";
let name = employee[preferedName]; // not possible with name.preferedName
```

With square brackets, you can even have dynamic property names:

```javascript
let name = "firstname";
let employee = {[name]: "Koray"};  // {firstname: "Koray"} - This is crazy..
```

### Functions in Objects
Objects can have functions as properties too:

```javascript
let user = {
    greet: function() {
        return "Greetings!";
    }
};
```

The _method_ above can be called via:

```javascript
user.greet();
```

There is a shorthand form for functions in objects:

```javascript
let user = {
    greet() {
        return "Greetings!";
    }
};
```

## Functions
JavaScript is very liberal in terms of calling functions. If you pass too many functions, extra ones will be ignored. If you pass too few, missing parameters get assigned `undefined`. This allow functions to be called with different number of arguments as follows:

```javascript
function minus(a, b) {
    if (b === undefined)
        return -a; 
    else 
        return a - b;
}
```

### Default Value for Parameters in Functions

```javascript
function power(base, exponent = 2) {
    // You already understand..
}
```