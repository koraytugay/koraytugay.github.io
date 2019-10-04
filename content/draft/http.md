---
layout: default
title:  "HTTP"
---

# HTTP
{:.no_toc}

* TOC
{:toc}

## Overview
HTTP stands for Hypertext Transfer Protocol but is used for transferring all kinds of data and it is a protocol based on plain text.

__HTTP Requests__ are composed of a domain (or an IP address), a resource, a method and usually a bunch of headers. Requests, in addition to headers, can have body too. A hello world in HTTP can be demonstrated by using `telnet example.com 80`:

```plaintext
Trying 93.184.216.34...
Connected to example.com.
Escape character is '^]'.
GET / HTTP/1.1
host: example.com
connection: close

HTTP/1.1 200 OK
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Sun, 29 Sep 2019 21:53:24 GMT
Etag: "1541025663+ident"
Expires: Sun, 06 Oct 2019 21:53:24 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (ord/573A)
Vary: Accept-Encoding
X-Cache: HIT
Content-Length: 1270
Connection: close

<!doctype html>
<html>
..
</html>
```

The example above shows `host` and `connection` headers. HTTP headers are __not__ case sensitive. There are many other HTTP headers. Following the domain comes the path and the query parameters. For example `/foo/bar/baz?q=1&t=2` consists of the path `/foo/bar/baz` and query parameters `q=1` and `t=2`.

As per methods, here is a summary: `GET` usually does not have any body and used to retrieve data. `POST` usually is used to insert data which is supplied in the request body. `HEAD` asks for HTTP headers only for a given path. `OPTIONS` is used mostly for CORS requests. `DELETE` is used for deleting resources, mostly found in a specific path such as `/v1/orders/id/42` would be a request to delete order with id 42. `PUT` is similar to `POST` but means either insert or update and `PATCH` is used for partial updates. 

`CONNECT` is a special HTTP method. Instead of making a request to a server directly, it asks for a proxy server to open a connection. Proxy servers usually immediatly answer `CONNECT` requests, and blindly forwards any request to destination and vise-versa. 

__HTTP Responses__ are very similar to HTTP Requests in the sense of they also have headers and a body but instead of methods, responses has statuses such as `200` or `404`.



## Proxy Server
Proxy servers sit between the client computer and the Internet and are used for security, filtering targets or contents and caching. 

```plaintext
+--------+      +-------+     +--------+      +--------+
|        +----->+       +---->+        +----->+        |
| Client |      | Proxy |     |Internet|      | Target |
|        +<-----+       +<----+        +<-----+        |
+--------+      +-------+     +--------+      +--------+
```

### HTTPS Connection over Proxy Server
SSL connection can be possible in the existence of proxy servers by turning the server into a TCP tunnel with `CONNECT`. More detailed answer can be found in [this](https://stackoverflow.com/a/40885184) answer and in [Section 4.3.6](https://tools.ietf.org/html/rfc7231#section-4.3.6) of Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content RFC. [Wikipedia](https://en.wikipedia.org/wiki/HTTP_tunnel#HTTP_CONNECT_method) also explains and gives examples on how `CONNECT` works.

## SSL Connection with openssl
`telnet` does not support secure connections, however `open_ssl` can be used from terminals to connect to web servers that require the handshake.

```bash
openssl s_client -connect examplecat.com:443
``` 

will successfuly handle the required SSL handshake by the server:

```plaintext
CONNECTED(00000005)
depth=2 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert Global Root CA
verify return:1
depth=1 C = US, O = DigiCert Inc, CN = DigiCert SHA2 Secure Server CA
verify return:1
depth=0 C = US, ST = ca, L = San Francisco, O = "Netlify, Inc", CN = *.netlify.com
verify return:1
---
Certificate chain
 0 s:/C=US/ST=ca/L=San Francisco/O=Netlify, Inc/CN=*.netlify.com
   i:/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
 1 s:/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
   i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert Global Root CA
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIGIzCCBQugAwIBAgIQC1W/C9syOFclqIEumW8/STANBgkqhkiG9w0BAQsFADBN
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMScwJQYDVQQDEx5E
...
-----END CERTIFICATE-----
subject=/C=US/ST=ca/L=San Francisco/O=Netlify, Inc/CN=*.netlify.com
issuer=/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
---
No client certificate CA names sent
Server Temp Key: ECDH, X25519, 253 bits
---
SSL handshake has read 3407 bytes and written 293 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES128-GCM-SHA256
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES128-GCM-SHA256
    Session-ID: 49A706EFE3CF2A507063E23CD8970517317959AF3C207BF624EF91F2CC79A216
    Session-ID-ctx: 
    Master-Key: 126A134A751750DE5229283CAA12B010AC711FD06DA431CBA2A63378E81FF41DB3C96154FE64302D9212F4650B48D351
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 18 cb 40 a9 17 a3 86 e8-55 2a f8 df fd 32 ad b8   ..@.....U*...2..
    0010 - ae 01 05 52 77 e5 64 9f-bb d7 e7 13 b9 06 8b 79   ...Rw.d........y
    0020 - d4 c4 55 5c 04 31 5c 27-9d 6d 77 e1 03 30 6d 8e   ..U\.1\'.mw..0m.
    Start Time: 1569174426
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
---
```
At this point we are free to send HTTP requests, that are secured by `openssl` as per the SSL handshake. Sending the following followed by two linebreaks:

```bash
GET /cat.txt HTTP/1.1
host: examplecat.com
connection: close
```

will return:

```plaintext
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: public, max-age=0, must-revalidate
Content-Length: 33
Content-Type: text/plain; charset=UTF-8
Date: Sun, 22 Sep 2019 17:48:56 GMT
Etag: "ac5affa59f554a1440043537ae973790-ssl"
Strict-Transport-Security: max-age=31536000
Age: 31
Connection: close
Server: Netlify
X-NF-Request-ID: 430113ee-49ad-4d0f-ae15-c270b9635100-1238620

\    /\
 )  ( ')
(  /  )
 \(__)|
closed
```

## Random Notes
- [SSL Labs](https://www.ssllabs.com/ssltest/viewMyClient.html) offers a webpage where you can check what SSL/TLS protocols your browser supports.
- [curl](https://curl.haxx.se/) and [wget](https://www.gnu.org/software/wget/) are useful tools for playing with HTTP APIs.
- [netcat](http://netcat.sourceforge.net/) is very useful for listening on ports. To listen on port 5000: `netcat -l -p 5000`.
- [howhttps.works](https://howhttps.works/) is fun to go through.