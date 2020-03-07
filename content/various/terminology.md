---
layout: default
title:  "Terminology"
---

# Terminology
{:.no_toc}

## A
__Abstraction__ A simplification of something much more complicated that is going on under the covers. As it turns out, a lot of computer programming consists of building abstractions. What is a string library? It’s a way to pretend that computers can manipulate strings just as easily as they can manipulate numbers. What is a file system? It’s a way to pretend that a hard drive isn’t really a bunch of spinning magnetic platters that can store bits at certain locations, but rather a hierarchical system of folders-within-folders containing individual files that in turn consist of one or more strings of bytes. _[Joel on Software - The Law of Leaky Abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)_

## D
__DevOps__ DevOps is a cultural sensibility. It's a unified, metrics based approach to making software efficiently and fearlessly using as much automation as possible. _Some cartoon shared in a chat room_

## E
__Expression__ A fragment of code that produces a value is called an expression.

## H
__Higher Order Functions__ Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions. _[Eloquent JavaScript](https://eloquentjavascript.net/)_

## S
__Service__ Any processes, functionality, or data that must be discoverable and available over a network.

## T
__Transaction__ A transaction is a set of one or more statements that is executed as a unit, so either all of the statements are executed, or none of the statements is executed. _[The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/jdbc/basics/transactions.html)_

Why you may want to manage your own transactions is to maintain the integrity of your business processes. For example, if a customer places an order for some merchandise, the information you'd need to store for that order would include a list of items to purchase, billing and shipping information, and an authorized credit card charge. This information would likely be stored in several different tables. Without a manually managed transaction, it's possible to enter part of the order, and then the system fails. The rest of the order information is then lost. [Java Programming with Oracle JDBC](http://shop.oreilly.com/product/9780596000882.do)

__Transaction Level Dirty Read__ A transaction with an isolation level that allows for dirty reads implies that within that transaction you can see changes to data not yet committed by other transactions. [Expert Oracle JDBC Programming](https://www.apress.com/gp/book/9781590594070)