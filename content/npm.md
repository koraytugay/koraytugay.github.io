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

### Scripts
`package.json` comes with a section called `scripts`. Add the following in `scripts` section:

```json
"scripts": {
  "start": "node index.js",
  "fire": "node index.js"
}
```

You can create artibrary scripts as seen above. In order to call `start` all you need to do is: `npm start`. However, `npm fire` will not work, it requires: `npm run fire`. With that said, you can also call `npm run start`.

Some platforms such as Heroku will look at the `start` script. You also will want to populate `start` so that you or any other developer can simply start your application with `npm start` instead of trying to figure out what the entry point is.

## Random Notes
- What is the `main` in `package.json`? [Here](https://stackoverflow.com/a/40792477) is the answer.
- Printing modules for a project:

```bash
npm list -depth=0
```

- Printing all globally installed modules, either of:

```bash
npm list -g -depth=0
ls $( npm root -g )
npm root -g | xargs ls
```

- 2 very useful modules: `nodemon` and `live-server`. Check them out.