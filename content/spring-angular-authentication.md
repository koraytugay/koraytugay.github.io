---
layout: default
title:  "Spring - Angular - Authentication"
---

# Spring - Angular - Authentication
{:.no_toc}

* TOC
{:toc}

## Overview
`sprangseccli` stands for _spring - angular - security - client_ and the other folder is the _server_.
This post is a summary of the important pieces in the flow. Runnable code can be found [here](https://github.com/koraytugay/spr-ang-sec), make sure to check different branches. 

## Basic HTTP authentication
Runnable code is at branch `http-basic` in [here](https://github.com/koraytugay/spr-ang-sec/tree/http-basic). 

[data.service.ts](https://github.com/koraytugay/spr-ang-sec/blob/http-basic/sprangseccli/src/app/data.service.ts) houses the method that sends Basic Authentication headers to `/validate` endpoint.

The form submission business logic and the outcome of the validation is found in [login.component.ts](https://github.com/koraytugay/spr-ang-sec/blob/http-basic/sprangseccli/src/app/login/login.component.ts). Every subsequent request then relies on the session cookie stored in the browser. This can be seen in [foo.component.ts](https://github.com/koraytugay/spr-ang-sec/blob/http-basic/sprangseccli/src/app/foo/foo.component.ts), where the request is made with `withCredentials: true`.

The required configuration on the backend is at [SecurityConfiguration](https://github.com/koraytugay/spr-ang-sec/blob/http-basic/sprangsecsrvr/src/main/java/biz/tugay/sprangsec/configuration/SecurityConfiguration.java). 

### Notes
- This configuration currently survives browser refresh, since the cookie is still in the browser. 
- This configuration does not survive a browser restart, the session cookie is only alive as long as the browser is open.
- Trick needed to hide basic authentication popup in Chrome when the credentials were wrong was to add:
```
headers.append("X-Requested-With", "XMLHttpRequest")
```
- Any REST client (such as Insomnia) can be used to send requests to backend with Basic Authentication. 
- Session in back-end holds the 

## JWT Authentication
This approach can be found in [jwt](https://github.com/koraytugay/spr-ang-sec/tree/jwt) branch. It is definetly a beast and not easy to digest. 

The heart of the logic is found at [JwtService](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangsecsrvr/src/main/java/biz/tugay/sprangsec/service/JwtService.java). This service is used by [JwtFilter](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangsecsrvr/src/main/java/biz/tugay/sprangsec/filter/JwtFilter.java). There is a lot of manual and hacky work done here to set authentication in Spring Security. [SecurityConfiguration](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangsecsrvr/src/main/java/biz/tugay/sprangsec/configuration/SecurityConfiguration.java) is slightly different.

The idea is to send basic authentication to to `/validate` endpoint (which is handled by [ValidateController](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangsecsrvr/src/main/java/biz/tugay/sprangsec/controller/ValidateController.java), but instead of acquiring a session (and keeping a session in backend), we get a Json Web Token. This token holds the information we would otherwise hold in session. Our username, and our role. 

This iteration stores the token in-memory in the browser. This can be observed in [auth.service.ts](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangseccli/src/app/auth.service.ts). Requests to back-end now needs to send this token with every request. An example can be seen in [foo.component.ts](https://github.com/koraytugay/spr-ang-sec/blob/jwt/sprangseccli/src/app/foo/foo.component.ts).

### Notes
- This approach does not survive browser refreshes. This is because the client application is re-loaded after a refresh, and the token stored in client is lost.
- This approach is good for multiple instances since there is no session in the server. All the information is sent from client in every request, which contains the username and the role user has.