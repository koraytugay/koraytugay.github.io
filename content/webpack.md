---
layout: default
title:  "webpack"
---

# Webpack
{:.no_toc}

* TOC
{:toc}

## Hello World
The following are examples that can both be called via `node ./dist/main` or by opening the `index.html` in the browser. 

```bash
npm install      # install webpack
npm run bundle   # let webpack bundle the files
node ./dist/main # run main.js within node

# Or open index.html via browser and open console
```

### Common Files

Directory layout

```
.
├── index.html
├── package.json
├── src
│   ├── a.js
│   └── index.js
└── webpack.config.js

1 directory, 5 files
```

package.json

```json
{
  "name": "my-webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "bundle": "webpack  --config webpack.config.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "webpack": "^4.42.1",
    "webpack-cli": "^3.3.11"
  }
}
```

webpack.config.js

```javascript
const path = require("path");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "main.js",
    path: path.resolve(__dirname, "dist"),
    library: "ui"
  }
};
```

index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Webpack!</title>
</head>
<body>
    <div>
      <button onclick="ui.a()">a</button>
    </div>
    <script src="./dist/main.js"></script>
</body>
</html>
```

### Variation Using ES6 Import - Export
a.js

```javascript
export function a() {
    console.log('hello world!');
};
```

index.js

```javascript
import { a } from './a';
export { a };

a();
```

### Variation Using Node Require - Exports
a.js

```javascript
module.exports = function a() {
    console.log('hello world!');
};
```

```javascript
let a = require('./a.js');

a();

module.exports = {
    a: a
}
```

