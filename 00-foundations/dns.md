# DNS

## 1. What Is DNS?

When you enter a website into a browser, you normally use a **domain
name**, such as:

``` text
www.example.com
```

Computers communicate using IP addresses, so the domain needs to be
translated into an IP address before the browser can communicate with
the server.

This process is called **DNS resolution**.

$$
\text{Domain Name} \xrightarrow{\text{DNS}} \text{IP Address}
$$

For example:

``` text
www.google.com
       ↓
      DNS
       ↓
216.58.201.228
```

The book uses this as **Step 2** in the process of visiting a website.

------------------------------------------------------------------------

## 2. Why Do We Need DNS?

A domain name is easier for people to use than an IP address.

Instead of remembering an address such as:

``` text
216.58.201.228
```

a user can visit:

``` text
www.google.com
```

DNS provides the mechanism for finding the IP address associated with
that domain.

Conceptually:

$$
\boxed{\text{Human-friendly domain}}
\rightarrow
\boxed{\text{DNS}}
\rightarrow
\boxed{\text{IP address}}
$$

------------------------------------------------------------------------

## 3. Domain Name → IP Address

When the browser determines the domain from a URL, the computer uses DNS
servers to find the corresponding IP address.

The book describes DNS servers as specialized servers that maintain a
registry mapping domains to their IP addresses.

For example:

$$
\texttt{www.google.com}
\rightarrow
\texttt{216.58.201.228}
$$

Once the IP address is known, the computer can attempt to establish a
TCP connection with that address.

So the overall flow is:

$$
\text{URL}
\rightarrow
\text{Domain}
\rightarrow
\text{DNS Resolution}
\rightarrow
\text{IP Address}
\rightarrow
\text{TCP Connection}
$$

------------------------------------------------------------------------

## 4. IPv4 and IPv6

The book introduces two versions of Internet Protocol:

### IPv4

IPv4 addresses contain four decimal numbers separated by periods.

Each number ranges from:

$$
0 \leq x \leq 255
$$

For example:

``` text
8.8.8.8
```

An IPv4 address contains 32 bits, giving:

$$
2^{32}
$$

possible address combinations.

### IPv6

IPv6 is the newer version of Internet Protocol and was designed in part
to address the exhaustion of available IPv4 addresses.

IPv6 addresses consist of groups of hexadecimal digits separated by
colons.

Example:

``` text
2001:4860:4860::8888
```

IPv6 addresses can be shortened using defined notation rules.

------------------------------------------------------------------------

## 5. Using `dig`

The book gives `dig` as a simple way to look up the IP address
associated with a domain.

For example:

``` bash
dig A example.com
```

The `A` record is used to look up an IPv4 address associated with the
domain.

This is useful during reconnaissance because a bug hunter frequently
needs to understand what infrastructure is associated with a target
domain.

------------------------------------------------------------------------

## 6. DNS in the Website-Visiting Process

DNS is only one part of what happens when you visit a website.

A simplified sequence from the book is:

``` text
1. Enter URL
       ↓
2. Extract domain
       ↓
3. Resolve domain using DNS
       ↓
4. Obtain IP address
       ↓
5. Establish TCP connection
       ↓
6. Send HTTP request
       ↓
7. Receive HTTP response
       ↓
8. Browser renders response
```

The important transition here is:

$$
\boxed{\text{Domain}}
\xrightarrow{\text{DNS}}
\boxed{\text{IP}}
$$

DNS allows the browser to move from a human-readable domain name to the
network address needed for communication.

------------------------------------------------------------------------

## 7. Why DNS Matters for Bug Bounty

DNS becomes especially relevant during **reconnaissance**.

A bug hunter may need to discover and understand:

-   Which domains belong to a target
-   Which subdomains exist
-   Which IP addresses domains resolve to
-   What infrastructure is exposed

The book later discusses **subdomain enumeration** as part of its
bug-bounty reconnaissance methodology.

For now, the key idea is simply:

> **DNS is the mechanism that helps translate domain names into IP
> addresses, allowing communication with the corresponding
> infrastructure.**

------------------------------------------------------------------------

## 8. Important Distinction

Do not think of DNS as the server itself.

DNS answers the question:

> **"What IP address is associated with this domain?"**

After the IP address is obtained, other protocols and services handle
the actual communication.

Conceptually:

$$
\text{DNS}
\rightarrow
\text{Find the IP}
$$

then:

$$
\text{TCP}
\rightarrow
\text{Establish communication}
$$

then:

$$
\text{HTTP/HTTPS}
\rightarrow
\text{Exchange web messages}
$$

------------------------------------------------------------------------

## 9. Key Takeaways

-   **DNS** stands for Domain Name System.
-   A domain name must be resolved to an IP address before the browser
    can communicate with the corresponding server.
-   DNS resolution can be represented as:

$$
\text{Domain Name} \rightarrow \text{IP Address}
$$

-   The book introduces both **IPv4** and **IPv6**.
-   IPv4 addresses use four decimal numbers separated by periods.
-   IPv6 addresses use hexadecimal groups separated by colons.
-   `dig A example.com` can be used to look up an IPv4 address.
-   DNS is an important foundation for understanding web reconnaissance.
-   DNS resolution happens before the TCP connection and HTTP request in
    the simplified website-visit process.

------------------------------------------------------------------------

## Source

*Real-World Bug Hunting: A Field Guide to Web Hacking* --- Chapter 1,
**Bug Bounty Basics**, "What Happens When You Visit a Website --- Step
1: Extracting the Domain Name" and "Step 2: Resolving an IP Address."
