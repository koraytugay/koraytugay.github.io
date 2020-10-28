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

## HttpBasic Authentication
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
