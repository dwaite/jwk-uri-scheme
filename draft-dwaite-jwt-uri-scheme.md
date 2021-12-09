%%%
title = "JWT URI Scheme"
abbrev = "jwt-uri"
category = "info"
ipr = "none"
workgroup = "todo"
keyword = ["jose", "jwk", "uri"]
docname = "draft-dwaite-jwt-uri-scheme"

[seriesInfo]
name = "Internet-Draft"
value = "draft-dwaite-jwk-uri-scheme"
status = "standard"

[venue]
group = "todo"
type = "independent"
github = "dwaite/jwt-uri-scheme"
latest = "https://dwaite.github.io/jwt-uri/scheme"

stand_alone = "yes"
smart_quotes = "no"

[pi]
toc = "yes"
sortrefs = "yes"
symrefs = "yes"

[[author]]
initials = "D."
surname = "Waite"
fullname = "David Waite"
email = "david+jwturi@alkaline-solutions.com"

%%%

.# Abstract

The goal is a URI scheme which represents a single cryptographic public key, leveraging the JOSE series of specifications

{mainmatter}

# Introduction

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Representation

A JWK URI is formatted as a key type and pairs of names and values, sorted in lexicographical order. The key type, names, and avalues are each delimited by colons. The characters for the key type as well as the names and values are limited to the character set allowed by BASE64URI.

```
   JWK = "jwk": <key-type> ":" [* <name> ":" <value>]
   key-type = + CHAR
   name = + CHAR
   value = + CHAR
   CHAR = [A-Za-z0-9\-_]
```
## Conversion from a JWK to a URI

A JWT URI represents the minimal set of values necessary to represent a public key for a given key type. This is equivalent to the set of values used for calculating a JWK Thumbprint [RFC7638]. From the example RSA key in 3.1 of that specification:

``` json
{
    "kty": "RSA",
    "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx4cbbfAAt
          VT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMstn6
          4tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FD
          W2QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n9
          1CbOpbISD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINH
          aQ-G_xBniIqbw0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw",
    "e": "AQAB",
    "alg": "RS256",
    "kid": "2011-04-29"
}
```

As defined in "JSON Web Key (JWK)" [JWK] and "JSON Web Algorithms
(JWA)" [JWA], the required members for an RSA public key are:

*  "kty"
*  "n"
*  "e"

The kty parameter is used as the key-type value, while the "n" and "e" parameters are set as name/value pairs within the URI. These are used in lexigographical order, i.e. "e" and then "n". Note that the kty parameter and thus the resulting key-type value are both case-sensitive.

The corresponding URI is thus:

```
jwk:RSA:e:AQAB:n:0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx4
    cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMstn
    64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2Qvz
    qY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbISD08
    qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqbw0Ls1
    jF44-csFCur-kEgU8awapJzKnqDKgw
```

## Conversion to a JWK

The JWK URI can be converted by creating a JSON object, and adding a "kty" property with the key-type specified within the URI. Then, each name and value pair are added as a key and corresponding string value into the JWK.

The URI specified above, when converted back to a JWK, will yield:

``` json
{
    "kty": "RSA",
    "e": "AQAB",
    "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx4cbbfAAt
          VT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMstn6
          4tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FD
          W2QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n9
          1CbOpbISD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINH
          aQ-G_xBniIqbw0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw"
}
```

# Security Considerations

Ellaborate: No limiting attributes, must be determined by context
  * "use"
  * "key_ops"
  * "alg"

Ellaborate: No trust mechanism
  * e.g. no "x5u" / "x5c" / "x5t"

# IANA Considerations

* Register "jwk" uri

{backmatter}

# Acknowledgments
{:numbered="false"}
