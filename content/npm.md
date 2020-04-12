---
layout: default
title:  "npm"
---

# npm
{:.no_toc}

* TOC
{:toc}

## Overview
A package manager for Javascript, similar to pip in Python and gems in Ruby. npm comes together with node.js but does not require any node.js skills to use, and can be used without node.js. But it requires node.js to be installed for itself to function properly.

`package.json` is the core of the npm. The `package.json` file can easily be created with: `npm init`. This file:

- Acts as a manifest for your application
- Lists dependencies of your application
- Defines scripts that can be run with `npm run ...`

## Hello World
Start by initalising a project with the defaults:

```bash
npm init --yes
```

And add dependency `lodash`:

```bash
npm install lodash
```

Now make some toy implementation in `index.js`:

```javascript
const _  = require('lodash');
const numbs = [1, 6, 3, 5, 2, 4];
_.each(numbs, num => console.log(num));
```

Execute `index.js` by running:

```bash
node index.js
# 1
# 6
# 3
# 5
# 2
# 4
```

### Notes on Hello World
The application must be kept in repository without the `node_modules` folder. `node_modules` folder can always be re-constructed by npm with `npm install` on the root folder.

`require` is the way you import modules specific to `node.js`. The suggested way is to use `import` and `export` that come with ES6. 

## Case Studies

### nodemon


## Random Notes
- What is the `main` in `package.json`? [Here](https://stackoverflow.com/a/40792477) is the answer.
- Printing all globally installed modules, either of:

```bash
ls $( npm root -g )
npm root -g | xargs ls
```