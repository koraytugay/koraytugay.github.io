---
layout: default
title:  "Proxy Servers"
---

# Proxy Servers
{:.no_toc}

* TOC
{:toc}

## Proxy Server
Proxy servers sit between the client computer and the Internet and are used for security, filtering targets or contents and caching. HTTP proxy servers are both web servers and web clients. Proxy servers can be categorized as _forward proxy servers_ and _reverse proxy servers_ depending on where they are located. 

### Forward Proxy Servers
A forward proxy server is usually found at the exit point of a local network to control the traffic between the local network and the Internet. 

### Reverse Proxy Servers
A reverse proxy server (also named as a surrogate) typically assumes the name and IP address of the web server directly. In case of reverse proxy servers, a client would not be aware of connecting to a reverse proxy. 

## How a Proxy Server Gets a Traffic
### Client Configured Proxy
A client can intentionally configure a proxy server for whatever reason. A proxy server at the operating system level can be configured for a client, in which case all the HTTP traffic, including any browser running on the computer will use. In this situation, clients will be aware that they are behind a proxy server, and will act differently in some situations: For example, for a secure HTTP connection via HTTPS, client will first send a `CONNECT` to the Proxy Server, instead of a HTTP request.

#### SSL Connection Behind a Proxy Server
We can observe this behaviour by the following. Start listening on port `8443` using netcat:

```bash
nc -l -p 8843
``` 

And try to establish an HTTPS connecting to port 8843 using your browser by trying `https://localhost:8843`.

The content you will see in netcat will be giberish:

```plaintext
???h???\Ϡ???gTR@R?02??<J=?`?0?,?(?$??
?k9̨̩̪??????=5???/?+?'?#??  ?g3?E?</?A????
    ?6

??????
```

Now configure your operating system to use proxy for HTTPS at `localhost` on port `8844` and start listening on port `8844`:

```bash
nc -l -p 8844
```

Again, try establishing an HTTPS connection using your browser. This time you should be seeing a `CONNECT` request:

```plaintext
CONNECT clients2.google.com:443 HTTP/1.1
Host: clients2.google.com:443
Proxy-Connection: keep-alive
```

Knowing being behind a proxy server, the browser sends a `CONNECT` request first, to the proxy server, demanding the proxy server to act as a TCP tunnel instead of an HTTP proxy. 

SSL connection can be possible in the existence of proxy servers by turning the server into a TCP tunnel with `CONNECT`. More detailed answer can be found in [this](https://stackoverflow.com/a/40885184) answer and in [Section 4.3.6](https://tools.ietf.org/html/rfc7231#section-4.3.6) of Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content RFC. [Wikipedia](https://en.wikipedia.org/wiki/HTTP_tunnel#HTTP_CONNECT_method) also explains and gives examples on how `CONNECT` works.

### Network Interceptors
Network Interceptors are usually used in office locations or schools. The clients may be configured to use a proxy, or may be unaware. The proxy server resides before the Internet intercepting all requests.

## A Very Simple Proxy in Java
pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>biz.tugay</groupId>
    <artifactId>httpproxy</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>http-proxy</name>
    <url>koraytugay.github.io</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.10</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>9.4.10.v20180503</version>
            </plugin>
        </plugins>
    </build>
</project>
```

MiniProxy.java
    
```java
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClientBuilder;

import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.*;
import java.util.*;

@WebServlet(urlPatterns = "/")
public class MiniProxy extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest servletRequest,
                         HttpServletResponse servletResponse)
            throws IOException {
        HttpGet get = new HttpGet(servletRequest.getRequestURL().toString());
        get.setHeader("Connection", "Close");

        // Request headers from client to be forwarded to target server
        Enumeration<String> servletReqHeaders = servletRequest.getHeaderNames();
        while (servletReqHeaders.hasMoreElements()) {
            String header = servletReqHeaders.nextElement();
            get.addHeader(header, servletRequest.getHeader(header));
        }

        HttpClient httpClient = HttpClientBuilder.create().build();
        HttpResponse targetResponse = httpClient.execute(get);

        InputStream content = targetResponse.getEntity().getContent();
        BufferedReader rd = new BufferedReader(new InputStreamReader(content));
        String line;
        while ((line = rd.readLine()) != null)
            new PrintWriter(servletResponse.getWriter()).println(line);

        // Response headers from target server to client
        Arrays.stream(targetResponse.getAllHeaders()).forEach(header ->
                servletResponse.setHeader(header.getName(), header.getValue()));

        servletResponse.addHeader("My-Proxy-Header", "Hello-World!");
        servletResponse.addHeader("Proxy-Connection", "Close");
        servletResponse.flushBuffer();
    }
}
```