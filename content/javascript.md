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

### Cheat Sheet
- You can get a type of a variable using either of:

```javascript
let typeOfVar;

// both return strings
typeOfVar = typeof var  // using as an operator
typeOfVar = typeof(var) // as a function
```

- Result of `typeof null` is `"object"`. This is wrong, `null` is not an object, it is a special value with its special type. The error is kept in the language due to compatibility and was never fixed.
- Result of `typeof alert` returns `"function"`. However, this is not strictly true, although `alert` is a function, it is still of type `"object"`. `typeof` treats functions differently for convenience. 