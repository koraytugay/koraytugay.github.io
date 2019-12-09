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

```bash
telnet example.com 80
# Trying 2606:2800:220:1:248:1893:25c8:1946...
# Connected to example.com.
# Escape character is '^]'.
GET / HTTP/1.1
host: example.com
connection: close
 
# HTTP/1.1 200 OK
# Cache-Control: max-age=604800
# Content-Type: text/html; charset=UTF-8
# Date: Sun, 08 Dec 2019 19:06:34 GMT
# Etag: "3147526947+gzip+ident"
# Expires: Sun, 15 Dec 2019 19:06:34 GMT
# Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
# Server: ECS (dcb/7EEE)
# Vary: Accept-Encoding
# X-Cache: HIT
# Content-Length: 1256
# Connection: close
# 
# <!doctype html>
# <html>
# <head>
#     <title>Example Domain</title>
#     <meta charset="utf-8" />
#     <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
#     <meta name="viewport" content="width=device-width, initial-scale=1" />
# </head>
# <body>
# <div>
#     <h1>Example Domain</h1>
#     <p>
#         This domain is for use in illustrative examples in documents. 
#         You may use this domain in without asking for  permission.
#     </p>
# </div>
# </body>
# </html>
# Connection closed by foreign host.
```

The example above shows `host` and `connection` headers. HTTP headers are __not__ case sensitive. There are many other HTTP headers. Following the domain comes the path and the query parameters. For example `/foo/bar/baz?q=1&t=2` consists of the path `/foo/bar/baz` and query parameters `q=1` and `t=2`.

As per methods, here is a summary: `GET` usually does not have any body and used to retrieve data. `POST` usually is used to insert data which is supplied in the request body. `HEAD` asks for HTTP headers only for a given path. `OPTIONS` is used mostly for CORS requests. `DELETE` is used for deleting resources, mostly found in a specific path such as `/v1/orders/id/42` would be a request to delete order with id 42. `PUT` is similar to `POST` but means either insert or update and `PATCH` is used for partial updates. 

`CONNECT` is a special HTTP method. Instead of making a request to a server directly, it asks for a proxy server to open a connection. Proxy servers usually immediatly answer `CONNECT` requests, and blindly forwards any request to destination and vise-versa. 

__HTTP Responses__ are very similar to HTTP Requests in the sense of they also have headers and a body but instead of methods, responses has statuses such as `200` or `404`.

## A Brief History of HTTP Versions
### HTTP/0.9
The first published specification for HTTP was version 0.9. This specification basically stated a connection would be made over TCP/IP or a simmilar protocol to a server on an optional port, 80 being the default if no port is specified. A single line of ASCII text should be sent consisting of `GET` and the document address, followed by a new line. It is also mentioned that _Requests are idempotent. The server need not store any information about the request after connection is closed._ This part is what makes HTTP _stateless_. 

There was no concept of HTTP headers or any other media, such as images. A typical HTTP/0.9 request would then be as follows:

```bash
GET /page.html

```

### HTTP/1.0
This version added some key features, including:
- More Request methods such as `HEAD` and `POST`, in addition to `GET` which already existed.
- Sending the HTTP version, in the request.
- HTTP headers, which could be sent with both the request and the response to provide meta-data about either the request or the response.
- A three-digit response code for responses, such as `404` for Not found, informing the client about the status of their request.
- A HTTP Request was full when it had two new lines instead of one, to indicate HTTP Headers were finished.
- [Persistent Connections](https://en.wikipedia.org/wiki/HTTP_persistent_connection).

These options made adding exchanging different types of media such as images by using the HTTP headers to define the content type being sent.

HTTP/1.0, in addition to specifying some standard headers, also allowed custom headers to be sent, however mentioning a server might ignore such headers, whereas standard headers should be processed.

At one time, there was a convention to include an `X-` in custom headers to show they are not standard, but this is being dropped. 

HTTP headers are specified by with a header name, a colon and then the value. The header name is case-insensitive but not the value. It is also possible to send multiple values using the same header.

Here is a typical HTTP/1.0 request:

```bash
GET /page.html HTTP/1.0
Accept: text/html
Accept-Language: en-GB
Connection: keep-alive
Host: www.cat.com
User-Agent: Netscape-Navigator
X-Items-To-Buy: Apple
X-Items-To-Buy: Banana


```

### HTTP/1.1
The significant differences that were introduced with HTTP/1.1 were the mandatory host header and making persistent connections the default which was introduced with HTTP/1.0 yet being optional. 

Making the host header mandatory was important for virtual servers, since servers were hosting multiple web pages, a connection was not enough to distinguish from which application a resource was being requested.

Eventhough it is the default behaviour, still many HTTP clients and server exchange the `connection: keep-alive` header in HTTP messages.

Other important features introduced with this version are:
- New methods such as `PUT`, `OPTIONS`, `CONNECT`, `TRACE` and `DELETE`
- Proxy support
- Authentication

## A Brief Overview of TCP Connections
Before HTTP requests and responses can be sent back and forth, a TCP connection must be opened between the client and the server. TCP gives HTTP a reliable bit pipe. _Bytes stuffed in one side of a TCP connection come out the other side correctly, and in the right order._

A computer might have several TCP connections open at any one time. TCP keeps all these connections straight through port numbers. A TCP connection is distinguished by four values:

- Source IP address
- Source port number
- Destination IP address
- Destination port number

Together, these four values uniquely define a connection. Two different TCP connections are not allowed to have the same values for all four address components, but _different connections can have the same values for some of the components_.

### Parallel Connections
Concurrent HTTP requests across multiple TCP connections. In practice, browsers do use parallel connections, but they limit the total number of parallel connections to a small number (often four). Servers are free to close excessive connections from a particular client.

### Persistent Connections
Reusing TCP connections to eliminate connect/close delays. TCP connections that are kept open after transactions complete are called persistent connections. Nonpersistent connections are closed after each transaction. Persistent connections stay open across transactions, until either the client or the server decides to close them.

### Parallel vs Persistent Connections
Parallel connections can speed up the transfer of composite pages but have some disadvantages:

- Each transaction opens/closes a new connection, costing time and bandwidth.
- Each new connection has reduced performance because of TCP slow start.
- There is a practical limit on the number of open parallel connections.

Persistent connections offer some advantages over parallel connections: They reduce the delay and overhead of connection establishment, keep the connections in a tuned state, and reduce the potential number of open connections. However, persistent connections need to be managed with care, or you may end up accumulating a large number of idle connections, consuming local resources and resources on remote clients and servers. Persistent connections can be most effective when used in conjunction with parallel connections. _Today, many web applications open a small number of parallel connections, each persistent._ 

### Pipelined connections
HTTP/1.1 permits optional request pipelining over persistent connections. This is a further performance optimization over keep-alive connections. Multiple requests can be enqueued before the responses arrive. While the first request is streaming across the network to a server on the other side of the globe, the second and third requests can get underway.

## Introduction to HTTPS
HTTPS instead of using TCP connections directly, uses TLS (also widely refered as SSL or SSL/TLS) on top of TCP. HTTPS adds encryption of the data being exchanged, integrity of the data being exchanged and authentication of the server being connected to.

HTTPS works by using public key encryption, which allows servers to provide public keys in the form of digital certificates when users first connect. The certificates are issued and signed by certificate authorities (known as CA) that are trusted by the client, hence the client can know the certificate it receives is genuine. 

When the client connects to an HTTPS server, a TLS handshake takes place consisting of agreeing on the encryption method to use and exchanging an encryption key. The public key cryptography is only used during the TLS handshake, once it is complete, a faster encryption is used.

### SSL Connection with openssl
telnet does not support secure connections, however open_ssl can be used from terminals to connect to web servers that require the handshake.

```bash
openssl s_client -connect examplecat.com:443
``` 

will successfuly handle the required SSL handshake by the server:

```bash
# CONNECTED(00000005)
# depth=2 C = US, O = DigiCert, OU = digicert.com, CN = DigiCert Global Root CA
# verify return:1
# depth=1 C = US, O = DigiCert, CN = DigiCert SHA2 Secure Server CA
# verify return:1
# depth=0 C = US, ST = ca, L = San Francisco, O = "Netlify", CN = *.netlify.com
# verify return:1
# ---
# Certificate chain
#  0 s:/C=US/ST=ca/L=San Francisco/O=Netlify, Inc/CN=*.netlify.com
#    i:/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
#  1 s:/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
#    i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert Global Root CA
# ---
# Server certificate
# -----BEGIN CERTIFICATE-----
# MIIGIzCCBQugAwIBAgIQC1W/C9syOFclqIEumW8/STANBgkqhkiG9w0BAQsFADBN
# MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMScwJQYDVQQDEx5E
# ...
# -----END CERTIFICATE-----
# subject=/C=US/ST=ca/L=San Francisco/O=Netlify, Inc/CN=*.netlify.com
# issuer=/C=US/O=DigiCert Inc/CN=DigiCert SHA2 Secure Server CA
# ---
# No client certificate CA names sent
# Server Temp Key: ECDH, X25519, 253 bits
# ---
# SSL handshake has read 3407 bytes and written 293 bytes
# ---
# New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES128-GCM-SHA256
# Server public key is 2048 bit
# Secure Renegotiation IS supported
# Compression: NONE
# Expansion: NONE
# No ALPN negotiated
# SSL-Session:
#     Protocol  : TLSv1.2
#     Cipher    : ECDHE-RSA-AES128-GCM-SHA256
#     Session-ID: 49A706EFE3CF2A507063E23CD8970517317959AF3C207BF624EF91F2CC79A
#     Session-ID-ctx: 
#     Master-Key: 126A134A751750DE5229283CAA12B010AC711FD06DA431CBA2A63378E81FF
#     TLS session ticket lifetime hint: 7200 (seconds)
#     TLS session ticket:
#     0000 - 18 cb 40 a9 17 a3 86 e8-55 2a f8 df fd 32 ad b8   ..@.....U*...2..
#     0010 - ae 01 05 52 77 e5 64 9f-bb d7 e7 13 b9 06 8b 79   ...Rw.d........y
#     0020 - d4 c4 55 5c 04 31 5c 27-9d 6d 77 e1 03 30 6d 8e   ..U\.1\'.mw..0m.
#     Start Time: 1569174426
#     Timeout   : 7200 (sec)
#     Verify return code: 0 (ok)
# ---
```
At this point we are free to send HTTP requests, that are secured by `openssl` as per the SSL handshake. Sending the following followed by two linebreaks:

```bash
GET /cat.txt HTTP/1.1
host: examplecat.com
connection: close
```

will return:

```bash
# HTTP/1.1 200 OK
# Accept-Ranges: bytes
# Cache-Control: public, max-age=0, must-revalidate
# Content-Length: 33
# Content-Type: text/plain; charset=UTF-8
# Date: Sun, 22 Sep 2019 17:48:56 GMT
# Etag: "ac5affa59f554a1440043537ae973790-ssl"
# Strict-Transport-Security: max-age=31536000
# Age: 31
# Connection: close
# Server: Netlify
# X-NF-Request-ID: 430113ee-49ad-4d0f-ae15-c270b9635100-1238620
# 
# \    /\
#  )  ( ')
# (  /  )
#  \(__)|
# closed
```

## Proxy Servers
Proxy servers sit between the client computer and the Internet and are used for security, filtering targets or contents and caching. HTTP proxy servers are both web servers and web clients. Proxy servers can be categorized as _forward proxy servers_ and _reverse proxy servers_ depending on where they are located. 

### Forward Proxy Servers
A forward proxy server is usually found at the exit point of a local network to control the traffic between the local network and the Internet. 

### Reverse Proxy Servers
A reverse proxy server (also named as a surrogate) typically assumes the name and IP address of the web server directly. In case of reverse proxy servers, a client would not be aware of connecting to a reverse proxy. 

### How a Proxy Server Gets a Traffic
#### Client Configured Proxy
A client can intentionally configure a proxy server for whatever reason. A proxy server at the operating system level can be configured for a client, in which case all the HTTP traffic, including any browser running on the computer will use. In this situation, clients will be aware that they are behind a proxy server, and will act differently in some situations: For example, for a secure HTTP connection via HTTPS, client will first send a `CONNECT` to the Proxy Server, instead of a HTTP request.

##### SSL Connection Behind a Proxy Server
We can observe this behaviour by the following. Start listening on port `8443` using netcat:

```bash
nc -l -p 8843
``` 

And try to establish an HTTPS connecting to port 8843 using your browser by trying `https://localhost:8843`.

The content you will see in netcat will be giberish:

```bash
# ???h???\Ϡ???gTR@R?02??<J=?`?0?,?(?$??
# ?k9̨̩̪??????=5???/?+?'?#??  ?g3?E?</?A????
#     ?6
# 
# ??????
```

Now configure your operating system to use proxy for HTTPS at `localhost` on port `8844` and start listening on port `8844`:

```bash
nc -l -p 8844
```

Again, try establishing an HTTPS connection using your browser. This time you should be seeing a `CONNECT` request:

```bash
# CONNECT clients2.google.com:443 HTTP/1.1
# Host: clients2.google.com:443
# Proxy-Connection: keep-alive
```

Knowing being behind a proxy server, the browser sends a `CONNECT` request first, to the proxy server, demanding the proxy server to act as a TCP tunnel instead of an HTTP proxy. 

SSL connection can be possible in the existence of proxy servers by turning the server into a TCP tunnel with `CONNECT`. More detailed answer can be found in [this](https://stackoverflow.com/a/40885184) answer and in [Section 4.3.6](https://tools.ietf.org/html/rfc7231#section-4.3.6) of Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content RFC. [Wikipedia](https://en.wikipedia.org/wiki/HTTP_tunnel#HTTP_CONNECT_method) also explains and gives examples on how `CONNECT` works.

#### Interceptors (aka Transparent Proxies)
There are several techniques where the network infrastructure intercepts and steers web traffic into a proxy, without the client’s knowledge or participation. This interception typically relies on switching and routing devices that watch for HTTP traffic, intercept it, and shunt the traffic into a proxy, without the client’s knowledge.

In such cases, clients computers also have certificates installed that trust the proxy server, and the proxy server becomes a man-in-the-middle. See [this](https://crypto.stackexchange.com/a/76232) answer for details.

#### Reverse Proxies
Servers (instead of the clients) can be behind proxy servers for again, whatever reason, such as load-balancing, filtering or authentication. In this case the client again would be unaware of being connected to a proxy server, but it is much more innocent compared to interceptors.

## Random Notes
- [SSL Labs](https://www.ssllabs.com/ssltest/viewMyClient.html) offers a webpage where you can check what SSL/TLS protocols your browser supports.
- [curl](https://curl.haxx.se/) and [wget](https://www.gnu.org/software/wget/) are useful tools for playing with HTTP APIs.
- [netcat](http://netcat.sourceforge.net/) is very useful for listening TCP ports.
- [howhttps.works](https://howhttps.works/) is fun to go through.