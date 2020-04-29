---
layout: default
title:  "Cracking the Coding Interview"
---

# Cracking the Coding Interview
{:.no_toc}

* TOC
{:toc}

## Overview
**Do not see these notes or solutions before attempting the challenges yourself.** These notes are for my own use and for possible discussions with people who have already solved them.

I do not include the problem statements here anyway hence this page itself can have very little value if you do not have the book anyway. Note the book itself has much more additional information compared to my own poor attempts here. I would highly recommend this book. 

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

#### Notes
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

#### Notes
Another solution that can be optimised and/or made cleaner and requires clarifications to be asked:

- Are white spaces considered? Is `go to` a permutation of `goot`?
- How about character cases, is `GOTO` a permutation of `goot`?

Assuming whitespace is significant (which means `go to` is **not** a permutation of `goot`) and the characters are case-sensitive, the first check would be to compare string lengths. If strings are not the same length, they cannot be permutations. 

A cleaner (yet less optimized) way then to implement this requirement would be to sort the character array of the strings and then comparing the two arrays. 

### URLify

This one is pretty straight forward. Things to keep in mind:

- Sometimes you should be looping from end to start. 
- The last element of a string is at index `length - 1`.
- You should loop while `i >= 0`.

```javascript
function urlify(charArr, trueLength) {
    let numberOfSpaces = 0;

    for (let i = 0; i < trueLength; i++) {
        if (charArr[i] === ' ') {
            numberOfSpaces++;
        }
    }

    let j = numberOfSpaces * 2 + trueLength - 1;
    for (let i = trueLength - 1; i >= 0; i--) {
        if (charArr[i] !== ' ') {
            charArr[j] = charArr[i];
            j--;
        } else {
            charArr[j] = '0';
            charArr[j - 1] = '2';
            charArr[j - 2] = '%';
            j = j - 3;
        }
    }

    return charArr;
}

module.exports = {
    urlify
};
```

### Palindrome Permutation

#### Clarification Questions

- Is the characterset limited?
- Are all characters lowercase or not? 
- Is palindrome calculation case-sensitive or not?

Since we are only practicing, lets assume all characters are already lowercase and the character set is limited to ASCII characters.

My solution is quite different than the ones in the book since I used a `Set`. 

```javascript
function palindromePermutation(string) {
    let chars = new Set();

    for (let c of string) {
        if (c === ' ') {
            continue;
        }
        if (chars.has(c)) {
            chars.delete(c);
        } else {
            chars.add(c);
        }
    }

    return chars.size < 2;
}

module.exports = {
    palindromePermutation
};

```