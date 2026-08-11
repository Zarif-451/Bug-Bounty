# TCP and Ports

## 1. What Is TCP?

**TCP (Transmission Control Protocol)** is a protocol that defines how
computers communicate with each other.

In the book's simplified website-visit process, TCP is used **after the
domain has been resolved to an IP address and before the HTTP request is
sent**.

The sequence is:

$$
\text{Domain}
\xrightarrow{\text{DNS}}
\text{IP Address}
\xrightarrow{\text{TCP}}
\text{Connection}
\xrightarrow{\text{HTTP}}
\text{Request}
$$

------------------------------------------------------------------------

## 2. Establishing a TCP Connection

After the browser obtains the server's IP address, it attempts to
establish a TCP connection with that IP address.

For a normal:

``` text
http://
```

connection, the book describes the browser as connecting to **port 80**.

For:

``` text
https://
```

the standard port described is **443**.

The important idea is that TCP provides a communication channel between
the client and the server.

------------------------------------------------------------------------

## 3. What Does TCP Provide?

The book keeps the details of TCP deliberately high-level.

For bug bounty purposes at this point, the important concept is that TCP
provides **two-way communication** between the computers and helps
ensure that information received is not lost or corrupted during
transmission.

You do **not** need to understand the internal details of TCP yet to
follow the book's web-hacking material.

Think of TCP as establishing the communication path over which the HTTP
messages can travel.

$$
\boxed{\text{Client}}
\;\xleftrightarrow{\text{TCP}}\;
\boxed{\text{Server}}
$$

------------------------------------------------------------------------

## 4. What Is a Port?

A server can run multiple services at the same time.

A **port** helps identify which service or process should receive
incoming network communication.

A useful mental model is:

> **An IP address identifies the computer/network destination, while a
> port identifies a particular service endpoint on that computer.**

Conceptually:

$$
\text{IP Address} + \text{Port}
\rightarrow
\text{Specific Service}
$$

For example:

``` text
216.58.201.228:80
```

means communicating with that IP on port `80`.

------------------------------------------------------------------------

## 5. Common Web Ports

The book introduces two important standard ports:

     Port Common use
  ------- ------------
     `80` HTTP
    `443` HTTPS

So, conceptually:

$$
\text{HTTP} \rightarrow \text{Port }80
$$

$$
\text{HTTPS} \rightarrow \text{Port }443
$$

These are **standard ports**, not strict requirements.

A service can be configured to communicate on a different port.

For example, HTTP could technically run on another port if an
administrator configures the application that way.

------------------------------------------------------------------------

## 6. Why Do Servers Need Ports?

Imagine one computer is running several services:

``` text
Server
│
├── Web service
├── SSH service
├── Database service
└── Other services
```

The server needs a way to distinguish traffic intended for each service.

Ports provide that distinction.

Conceptually:

$$
\text{Incoming Traffic}
\rightarrow
\begin{cases}
\text{Port 80} &\rightarrow \text{HTTP service}\\
\text{Port 443} &\rightarrow \text{HTTPS service}\\
\text{Other ports} &\rightarrow \text{Other services}
\end{cases}
$$

This becomes particularly relevant during security reconnaissance
because exposed services can reveal additional parts of a target's
attack surface.

------------------------------------------------------------------------

## 7. Ports as "Doors"

A useful analogy from the book is to think of ports as **doors to the
Internet**.

A server may have many doors, with different services listening behind
them.

For example:

``` text
                 Server
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
      :80        :443       :other
      HTTP       HTTPS      Service
```

The IP address gets you to the computer; the port helps direct the
communication to the appropriate service.

------------------------------------------------------------------------

## 8. Testing a TCP Connection

The book demonstrates that you can establish a TCP connection manually
using **Netcat (`nc`)**.

Example:

``` bash
nc <IP_ADDRESS> 80
```

This attempts to create a network connection to the specified IP on port
`80`.

Netcat can read and write messages over the connection.

For example:

``` bash
nc 216.58.201.228 80
```

The purpose here is not to memorize the command, but to understand that
network connections can be interacted with directly rather than only
through a browser.

------------------------------------------------------------------------

## 9. TCP's Position in Web Communication

At this stage, keep the following sequence in mind:

``` text
URL
 ↓
Domain
 ↓
DNS resolution
 ↓
IP address
 ↓
TCP connection
 ↓
HTTP request
 ↓
HTTP response
```

Or mathematically:

$$
\text{URL}
\rightarrow
\text{Domain}
\rightarrow
\text{DNS}
\rightarrow
\text{IP}
\rightarrow
\text{TCP}
\rightarrow
\text{HTTP}
$$

This is the important relationship between the three foundation files
you've studied so far:

``` text
client-server.md
       ↓
dns.md
       ↓
tcp-and-ports.md
       ↓
http.md
```

------------------------------------------------------------------------

## 10. Why TCP and Ports Matter for Bug Bounty

You will not normally exploit TCP itself when doing web bug bounty.

Instead, TCP and ports help you understand **where web services are
exposed**.

During reconnaissance, you may encounter:

``` text
example.com:80
example.com:443
example.com:8080
example.com:8443
```

The port can provide a clue about what service or application is
accessible there.

This is why basic knowledge of ports is useful even when your main focus
is web application security.

------------------------------------------------------------------------

## 11. What You Do NOT Need to Know Yet

For this book's Chapter 1, you do **not** need deep TCP knowledge.

You don't need to memorize:

-   TCP flags
-   TCP header fields
-   Sequence-number mechanics
-   Congestion-control algorithms
-   TCP state machines
-   Packet-level troubleshooting

Those are useful networking topics, but they are beyond the level needed
for this section of the book.

The book explicitly keeps the details of TCP at a high level here.

------------------------------------------------------------------------

## 12. Key Takeaways

-   **TCP** stands for Transmission Control Protocol.
-   TCP provides two-way communication between computers.
-   In the book's website-visit process, TCP is established after DNS
    resolution and before the HTTP request.
-   A **port** helps identify a service or process receiving network
    traffic.
-   `80` is the standard port associated with HTTP.
-   `443` is the standard port associated with HTTPS.
-   Standard ports are conventions; services can be configured to use
    other ports.
-   `nc` (Netcat) can be used to establish a TCP connection manually.
-   For bug bounty, understanding ports is useful during reconnaissance.
-   At this stage, conceptual TCP knowledge is sufficient.

------------------------------------------------------------------------

## Source

*Real-World Bug Hunting: A Field Guide to Web Hacking* --- Chapter 1,
**Bug Bounty Basics**, "What Happens When You Visit a Website --- Step
3: Establishing a TCP Connection."
