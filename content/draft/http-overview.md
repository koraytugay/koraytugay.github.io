---
layout: default
title:  "An overview of HTTP"
---

# An overview of HTTP
{:.no_toc}

* TOC
{:toc}

## Introduction
HTTP is a request / response application layer protocol used widely by the World Wide Web and RESTful applications. HTTP is not concerned with communication details, and is only concerned with the data and the meta-data being exchanged. Any application using HTTP for exchanging messages is responsible for opening the communication between the end-points, most widely these connections being TCP connections.

## A Brief History of HTTP Versions
### HTTP/0.9
The first published specification for HTTP was version 0.9. This specification basically stated a connection would be made over TCP/IP or a simmilar protocol to a server on an optional port, 80 being the default if no port is specified. A single line of ASCII text should be sent consisting of `GET` and the document address, followed by a new line. It is also mentioned that _Requests are idempotent. The server need not store any information about the request after connection is closed._ This part is what makes HTTP _stateless_. 

There was no concept of HTTP headers or any other media, such as images. A typical HTTP/0.9 request would then be as follows:

```plaintext
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

```plaintext
GET /page.html HTTP/1.0
Accept: text/html
Accept-Language: en-GB,en-US;q=0.8,en;q=0.6
Connection: keep-alive
Host: www.cat.com
User-Agent: Netscape-Navigator
X-Items-To-Buy: Apple
X-Items-To-Buy: Banana


```

### HTTP/1.1
The significant differences that were introduced with HTTP/1.1 were the mandatory host header and making persistent connections the defult which was introduced with HTTP/1.0 yet being optional. 

Making the host header mandatory was important for virtual servers, since servers were hosting multiple web pages, a connection was not enough to distinguish from which application a resource was being requested.

Eventhough it is the default behaviour, still many http clients and server exchange the `connection: keep-alive` header in HTTP messages.

Other important features introduced with this version are:
- New methods such as `PUT`, `OPTIONS`, `CONNECT`, `TRACE` and `DELETE`
- Proxy support
- Authentication

## Introduction to HTTPS
HTTPS instead of using TCP connections directly, uses TLS (also widely refered as SSL or SSL/TLS) on top of TCP. HTTPS adds encryption of the data being exchanged, integrity of the data being exchanged and authentication of the server being connected to.

HTTPS works by using public key encryption, which allows servers to provide public keys in the form of digital certificates when users first connect. The certificates are issued and signed by certificate authorities (known as CA) that are trusted by the client, hence the client can know the certificate it receives is genuine. 

When the client connects to an HTTPS server, a TLS handshake takes place consisting of agreeing on the encryption method to use and exchanging an encryption key. The public key cryptography is only used during the TLS handshake, once it is complete, a faster encryption is used.