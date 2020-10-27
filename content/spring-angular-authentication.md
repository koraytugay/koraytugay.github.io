---
layout: default
title:  "Spring - Angular - Authentication"
---

# Spring - Angular - Authentication
{:.no_toc}

* TOC
{:toc}

## HttpBasic Authentication
Runnable code can be found [here](https://github.com/koraytugay/spr-ang-sec/tree/http-basic). 
`sprangseccli` stands for _spring - angular - security - client_ and the other folder is the _server_.
This post is a summary of the important pieces in the flow.

`data.service.ts` consists of a single method that sends Basic Authentication headers to `/validate` endpoint:

```typescript
export class DataService {

  constructor(private http: HttpClient) {
  }

  validateUser(username: string, password: string): Observable<void> {
    const authData = btoa(`${username}:${password}`);  // binary to ascii - encodes input in base64
    const headers = new HttpHeaders().append('Authorization', 'Basic ' + authData);
    return this.http.get<void>('http://localhost:8080/validate',
      {headers: headers, withCredentials: true});
  }
}
```


