---
layout: default
title:  "React"
---

# React
{:.no_toc}

* TOC
{:toc}

## Overview
This page is a draft and is a work in progress...

## Notes
- What is Webpack?
Webpack is responsible for the build process, and one of the key steps is code transformation performed by the Babel package. 

- What is Babel?
Babel has two important tasks in a React project: transforming JSX content and transforming JavaScript code that uses the latest JavaScript features into code that can be executed by older browsers.

### Redux in a Nutshell

```javascript
import React, { Component } from 'react';

const counter = (state = { value: 0 }, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { value: state.value + 1 };
    case 'DECREMENT':
      return { value: state.value - 1 };
    default:
      return state;
  }
}

class Counter extends Component {
  state = counter(undefined, {});
  
  dispatch(action) {
    this.setState(prevState => counter(prevState, action));
  }

  increment = () => {
    this.dispatch({ type: 'INCREMENT' });
  };

  decrement = () => {
    this.dispatch({ type: 'DECREMENT' });
  };
  
  render() {
    return (
      <div>
        {this.state.value}
        <button onClick={this.increment}>+</button>
        <button onClick={this.decrement}>-</button>
      </div>
    )
  }
}
```