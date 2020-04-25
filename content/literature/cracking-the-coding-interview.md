---
layout: default
title:  "Cracking the Coding Interview"
---

# Cracking the Coding Interview
{:.no_toc}

* TOC
{:toc}

## Overview
Please do **not** see my notes or solutions before attempting the challenges yourself. These notes are for my own use and for possible discussions with people who have already solved them.

I will not be adding the problem definitions, and I must note the book itself has much more additional information compared to what I note here. I would highly recommend this book.

## Chapter 1
### Is Unique

**Solution**

```javascript
function isUnique(str) {
    for (let i = 0; i < str.length - 1; i++) {
        for (let j = i + 1; j < str.length; j++) {
            if (str.charAt(i) === str.charAt(j)) {
                return false;
            }
        }
    }
    return true;
}
```

**Notes**
My approach is somewhat poor and can be improved in several ways. 

The first things to ask is: _Is the character set limited to ASCII?_ If then, we can shortcut the function by returning `false` if string length is greater than 128.

Imagine we are allowed to use additional space, what options would we have? One could pass the string once, storing every character in a `Set` perhaps. But a `Set` maybe an overkill, again, if the character set is limited to ASCII. A boolean array, or even a `BitSet` should suffice. 

Can you implement solutions assuming the character set is in ASCII, using either a `boolean[]` or a `BitSet` as additional storage?

### Check Permutations

**Solution**

```javascript
function isPermutation(str, otherStr) {
    let charCount = new Map();

    for (let c of str) {
        if (!charCount.has(c)) {
            charCount.set(c, 1);
        } else {
            charCount.set(c, charCount.get(c) + 1);
        }
    }

    for (let c of otherStr) {
        if (!charCount.has(c)) {
            return false;
        }

        if (charCount.get(c) === 0) {
            return false;
        }        

        charCount.set(c, charCount.get(c) - 1);
    }

    for (let count of charCount.values()) {
        if (count !== 0) {
            return false;
        }
    }

    return true;
}
```

**Notes**
Another solution that can be optimised and/or made cleaner and requires clarifications to be asked:

- Are white spaces considered? Is `go to` a permutation of `goot`?
- How about character cases, is `GOTO` a permutation of `goot`?

Assuming whitespace is significant (which means `go to` is **not** a permutation of `goot`) and the characters are case-sensitive, the first check would be to compare string lengths. If strings are not the same length, they cannot be permutations. 

A cleaner (yet less optimized) way then to implement this requirement would be to sort the character array of the strings and then comparing the two arrays. 
