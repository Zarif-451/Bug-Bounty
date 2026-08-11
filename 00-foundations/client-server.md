# Client and Server

## 1. What Are Client and Server?

The **client** and **server** model describes how computers communicate
over the Internet.

-   **Client:** The computer that initiates a request.
-   **Server:** The computer that receives the request and provides a
    response.

The client can be a web browser, a command-line program, or another
application. In the context of this book, the server refers mainly to
websites and web applications.

> The important idea is not that a computer is permanently a "client" or
> "server." The role depends on whether it is initiating or receiving a
> particular request.

------------------------------------------------------------------------

## 2. Packets

Computers on the Internet communicate by sending **packets**.

A packet contains:

-   The data being transmitted
-   Information about where the data came from
-   Information about where the data is going

Conceptually:

$$
\text{Client} \rightarrow \text{Packets} \rightarrow \text{Server}
$$

For this book, the important part is the **data contained in the
packets**, particularly the HTTP messages. The details of packet-level
networking are outside the main focus here.

------------------------------------------------------------------------

## 3. Client-Server Communication

A simplified web communication looks like this:

$$
\boxed{\text{Client}}
\;\xrightarrow{\text{Request}}\;
\boxed{\text{Server}}
$$

The server processes the request and sends information back:

$$
\boxed{\text{Client}}
\;\xleftarrow{\text{Response}}\;
\boxed{\text{Server}}
$$

Together:

$$
\boxed{\text{Client}}
\;\xrightarrow{\text{Request}}\;
\boxed{\text{Server}}
\;\xrightarrow{\text{Response}}\;
\boxed{\text{Client}}
$$

For a website, the browser is commonly the client and the web
application is the server.

------------------------------------------------------------------------

## 4. Why Do They Need Common Rules?

The Internet contains many computers communicating with one another.
They therefore need agreed-upon standards that define how computers
should communicate.

The book introduces **RFCs (Request for Comments)** as documents that
define standards for how computers should behave.

One important example is:

> **HTTP --- Hypertext Transfer Protocol**

HTTP defines how a browser communicates with a remote server using
Internet Protocol (IP).

Both sides must follow the same communication standards so that they can
understand the messages being exchanged.

------------------------------------------------------------------------

## 5. HTTP in the Client-Server Model

HTTP sits at the center of web communication.

A simplified model is:

$$
\text{Browser}
\;\xrightarrow{\text{HTTP Request}}\;
\text{Web Server}
$$

and:

$$
\text{Web Server}
\;\xrightarrow{\text{HTTP Response}}\;
\text{Browser}
$$

For example, when a user visits a website, the browser prepares an HTTP
request and sends it to the appropriate server. The server then returns
an HTTP response.

This request/response interaction is the foundation for understanding
web applications and, consequently, web vulnerabilities.

------------------------------------------------------------------------

## 6. Why This Matters for Bug Bounty

As a bug hunter, you will frequently inspect the communication between a
client and server.

For example:

``` http
GET /profile HTTP/1.1
Host: example.com
```

The important question is:

> **What happens if the request is modified?**

A bug hunter may investigate whether changing a request parameter,
header, method, or other input causes the application to perform an
action it should not allow.

This is why understanding the client-server model is a foundation for
using tools such as **Burp Suite** later.

------------------------------------------------------------------------

## 7. Key Mental Model

Keep this model in mind:

$$
\boxed{\text{Client}}
\rightarrow
\boxed{\text{Request}}
\rightarrow
\boxed{\text{Server}}
\rightarrow
\boxed{\text{Response}}
\rightarrow
\boxed{\text{Client}}
$$

The client initiates communication.\
The server receives and processes the request.\
The server returns a response.

For web security, the **HTTP messages exchanged between them** are
especially important.

------------------------------------------------------------------------

## 8. Key Takeaways

-   A **client** typically initiates a request.
-   A **server** receives requests and responds to them.
-   Internet communication is carried through **packets**.
-   Packets contain data and addressing information.
-   **RFCs** define standards for Internet communication.
-   **HTTP** defines how a browser communicates with a remote web
    server.
-   In web security, understanding the **request/response interaction**
    is fundamental.
-   Later, bug hunting will involve examining and manipulating these
    HTTP communications.

------------------------------------------------------------------------

## Source

*Real-World Bug Hunting: A Field Guide to Web Hacking* --- Chapter 1,
**Bug Bounty Basics**, "Client and Server."
