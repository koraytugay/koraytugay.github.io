---
layout: default
title:  "JavaScript"
---

# JavaScript
{:.no_toc}

* TOC
{:toc}

## Data Types
### Strings
Remember backticks support new lines and inline variables..

```javascript
const myVar = "foo";

const backTicks = 
  `Backticks support new lines. 
   And inline variables: ${myVar}`;
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
JavaScript is very liberal in terms of calling functions. If you pass too many arguments, extra ones will be ignored. If you pass too few, missing parameters get assigned `undefined`. This allow functions to be called with different number of arguments:

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

## Global Object
The global object is provided by the runtime environment and it is the object accessed when you do not specify any specific objects. In browsers, global object is `window` whereas in node it is `global`. 

When you access the `Math` object directly in a browser environment, as in `Math.E`, you are actually accessing `window.Math`. 

There are many other properties that are very useful that comes with the global object, simply type `window` or `globalThis` in the console and see for yourself.

## Dark Side of the JavaScript
### Rest Parameters
To gather all passed arguments in an array, use the rest (`...`) operator. The dots literally mean _gather the remaining parameters into an array_.

```javascript
function authorTitles(firstname, lastname, ...titles) {
    return {
        firstname,
        lastname,
        titles
    }
}

let king = authorTitles("Stephen", "King", "christine", "it", "shining");
console.log(king);

// Prints the following, note how titles are in an array:
// { 
//     firstname: 'Stephen',
//     lastname: 'King',
//     titles: [ 'christine', 'it', 'shining' ] 
// }
```

The rest parameters must always be at the very end, as the last function parameter.

### Spread Syntax
Spread syntax looks very similar to the rest parameters, also using `...`, but does quite the opposite. It spreads the the values of an iterable to individual values:

```javascript
function greet(firstname, lastname) {
    console.log(`Hello ${lastname}. Can I call you ${firstname}?`);
}

greet(...["Koray", "Tugay"]);
// Hello Mr.Tugay. Can I call you Koray?
```

Note how values in the array has been expanded. 

### Array Destructuring
Destructuring assignment is a special syntax that allows us to “unpack” arrays or objects into a bunch of variables.

```javascript
let [firstname, lastname] = ["Koray", "Tugay"];
// firstname is set to "Koray"
// lastname is set to "Tugay"
```

Remember the example from spread syntax? Here is something similar to that:

```javascript
function greet([firstname, lastname]) {
    console.log(`Hello ${lastname}. Can I call you ${firstname}?`);
}

greet(["Koray", "Tugay"]);
// Hello Mr.Tugay. Can I call you Koray?
```

### Object destructuring
We can also destructure objects.. 

```javascript
let kt = {
    firstname: "Koray",
    lastname: "Tugay"
};

let { firstname, lastname } = kt;

// firstname is set to "Koray"
// lastname is set to "Tugay"
```

You can even destructure nested objects:

```javascript
let foo = {
    bar: "immediate value",
    nested: {
        baz: "nested value"
    }
};

let {
    bar,
    nested: {
        baz
    }
} = foo;

console.log(bar); // immediate value
console.log(baz); // nested value
```