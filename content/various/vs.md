---
layout: default
title:  "vs"
---

# vs

## Dynamically Typed vs Statically Typed
__Dynamically Typed__
- Type information is associated with __values__. 
- Variables __can__ refer to different types.

```python
# python is a Dynamically Typed language, hence the following is allowed:
x = 5
x = "koraytugay"
``` 
__Statically Typed__
- Type information is associated with __variables__. 
- Variables __cannot__ refer to different types.

```java
// Java is a Statically Typed language, hence the following is not allowed:
class _{
    int x = 5;
    // x = "koraytugay"; Not allowed!    
}
```

__References__
- [Static/Dynamic vs Strong/Weak](https://stackoverflow.com/a/34004765/1173112)

## Strongly Typed vs Weakly Typed
- No consensus on this matter.

__References__
- [What are the key aspects of a strongly typed language?](https://stackoverflow.com/a/122751/1173112)

## First Class Citizens vs Second Class Citizens

__First Class Citizens__
> .. note that the whole point of a programming language is to manipulate values, which, following historical programming-language tradition, are therefore called first-class values (or citizens)

__Second Class Citizens__
> .. other structures in programming languages, which are used for expressing the structure of values but which can't be passed around during program execution, are second-class citizens 

__References__
- [Modern Java in Action](https://www.manning.com/books/modern-java-in-action)

## Internationalization vs Localization

__Internationalization__ is the process of designing your program so it can be adapted. This involves placing strings in a property file and using classes like DateFormat so that the right format is used based on user preferences. 

__Localization__ means actually supporting multiple locales. Localization includes translating strings to different languages. It also includes outputting dates and numbers in the correct format for that locale. You can go through the localization process many times in the same application as you add more languages and countries.

Since internationalization and localization are such long words, they are often abbreviated as i18n and l10n. The number refers to the number of characters between the first and last characters, in other words, the number of characters that are replaced with a number.

__References__ 
- [OCP: Oracle Certified Professional Java SE 8 Programmer II Study Guide](https://www.wiley.com/WileyCDA/WileyTitle/productCd-1119067898,miniSiteCd-SYBEX.html)

## Internet vs World Wide Web
The internet is a collection of public computers linked through the shared use of the Internet Protocol (IP) to route messages. It’s made up of many services, including the World Wide Web, email, file sharing, and internet telephony.

HTTP/2 In Action