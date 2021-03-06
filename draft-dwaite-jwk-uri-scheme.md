%%%
title = "JWK URI Scheme"
abbrev = "jwk-uri"
category = "info"
ipr = "none"
workgroup = "todo"
keyword = ["jose", "jwk", "uri"]
docname = "draft-dwaite-jwk-uri-scheme"

[seriesInfo]
name = "Internet-Draft"
value = "draft-dwaite-jwk-uri-scheme"
status = "standard"

[venue]
group = "todo"
type = "independent"
github = "dwaite/jwk-uri-scheme"
latest = "https://dwaite.github.io/jwk-uri-scheme/"

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
email = "david+jwkuri@alkaline-solutions.com"

[[author]]
initials = "D."
surname = "Chadwick"
fullname = "David Chadwick"
email = "david+jwkuri@alkaline-solutions.com"

%%%

.# Abstract

The document specifies a URI key that represents a single cryptographic public key. This enables a key to be used directly in contexts which expect key lookup or naming via URI.

{mainmatter}

# Introduction

This document defines a URI scheme to directly identify a asymmetric key pair by public key. This scheme is meant to be trivially convertable into a JSON Web Key (JWK)[RFC7517], represented as a JavaScript Object Notation (JSON)[RFC8259] object.

This enables use of JWKs directly in environments which normally support URLS to indirectly resolve cryptographic material. An example usage of a JWK URI would be as the `iss` claim within a JSON Web Token (JWT) [RFC7519], allowing one to cryptographically verify the validity of a token without prior setup or resolution of keys through a network process.

A JWK URI is also defined in terms of a single canonical representation for a public key, using the same requirements as JWK Thumbprints [RFC7638]. This allows an identifier to be used to uniquely represent a public key, as well as allows for generation of JWK thumbprints from a JWK URI.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 [@!RFC2119] [@RFC8174]
when, and only when, they appear in all capitals, as shown here.

# Representation

A JWK URI represents a JSON-Based JWK containing only the required members of a JWK representing the public key, without any whitespace within the key fields. The JWK URI specification relies on two properties of this data:

1. The key types registered by the JWK specification, and the corresponding required member names for public keys for those key types, are entirely representable with the 'base64url' alphabet.
2. The data allowed for the required members consists entirely of base64url-encoded data.

As such, a JWK URI consists of pairs of member names and associated string values. Key type is serialized separately as it specifies the set of required members. Those required members are sorted lexicographically and colons are used as delimiters.

``` bnf
   JWK          = "jwk": <key-type> ":" [* <name> ":" <value>]
   key-type     = + CHAR
   name         = + (CHAR)
   value        = + (CHAR)
   CHAR         = [A-Za-z0-9\-_]
```

The use of the "." character as well as percent encoding are reserved for future use, such as the representation of a structured member data type if required by a future JWK key type.

# Conversion from a JWK to a URI

A JWK URI represents the minimal set of values necessary to represent a public key for a given key type. This is equivalent to the set of values used for calculating a JWK Thumbprint [RFC7638].

The `kty` member and the required members for that given key type should be extracted. The required members should be sorted by name into lexigographical order.

The JWK URI consists of the JWK URI scheme, name, the `kty` member value, and the pairs of member names and values, using the colon character as a separator.

## Validity

The JWK URI formed from a JWK JSON object is meant to be a canonical representation of a public key. A JWK is considered locally valid only if all of the following properties are met:

1. The `kty` and associated required member names are known
1. The URI does not end in a colon
1. There is no member name without a corresponding member value.
1. The key type or member names have correct capitalization. These values are defined by JWK as case sensitive.
1. The required members are all present
1. No members other than the required members are present
1. The `kty` parameter is not duplicated as a member
1. No invalid characters or percent encoding are used.
1. Member name:value pairs are in lexicographical order.

A JWK which is not locally valid MUST NOT be converted into JWK form, or otherwise used as a public key, in applicatiosn which expect canonical identifiers. 

Note that URI validity does not imply that the JWK meets other requirements for safe use. One example would be a JWK URI which represents an elliptic curve public key, where the point is not on the specified curve.

## Conversion to a JWK

A JWK URI can be converted into a JSON value by splitting the data on colons, extracting the second value as a `kty` member, and recgnizing subsequent values as pairs of member names and member values. The `kty` and other members, when added as properties to a JSON object, form a valid JWK.

## Example

From the example RSA key in 3.1 of the JWK specification:

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

As defined in "JSON Web Key (JWK)" [JWK] and "JSON Web Algorithms (JWA)" [JWA], the required members for an RSA public key are:

*  "kty"
*  "n"
*  "e"

The `kty` parameter is used as the `key-type` value in the URI, while the "n" and "e" member names sorted into lexicographical order, e.g. to "e" followed by "n". The member name and corresponding value are each added to the URI, delimited by colons.

The corresponding URI is thus:

```
jwk:RSA:e:AQAB:n:0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx4
    cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMstn
    64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2Qvz
    qY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbISD08
    qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqbw0Ls1
    jF44-csFCur-kEgU8awapJzKnqDKgw
```

This JWK URI can be converted back by extracting "RSA" as the `key-type` value, and adding it to the JSON object as the `kty` property. The remaining values, separated by colons, correspond to names and values. The `e` and `n` values are extracted and added to the JSON dictionary as the `e` and `n` properties. 

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

Note that this JWK does not have the `alg` or `kid` members which were present in the original JWK.

# Comparison with JWK Thumbprints

To validate a JWK Thumbprint, the receiver must have the corresponding public key available to them. When used as a key identifier or as a way of identifying the issuer of a message, this public key must be represented by a JWK and a thumbprint generated and compared as specified by [RFC7638]. If multiple public keys could potentially be used, the thumbprint of each key can be used to differentiate them.

For applications where the signer identity is primarily based on their public key, this creates the potential for insecure implementations. For example, [OpenID.Core] defines a Self-Issued OP feature where an id_token JWT is signed by the subject, and the subject is represented as a JWK thumbprint. The key itself is included within the JWT, as a `sub_jwk` claim holding a JWK JSON object. The token and underlying authentication protocol serve to prove the subject is the one who owns the key and is identified by the JWK thumbprint.

However, there is a risk that an implementer could use the thumbprint value as a local system identifier for the subject without verifying the included JWK, used to sign the message, actually corresponds to the specified thumbprint. This would allow anyone to impersonate a subject by signing the message with any valid key. Such oversights are difficult to detect without negative testing.

A JWK URI, if valid, is an identifier which itself represents the key. The above example could represent the subject as a URI holding the key, without either a thumbprint or duplicating the key structure into a `sub_jwk` claim. Since the key information is not derived or duplicated, signature validation should hinge on correect support for the JWK URI.

For this particular use case the JWK, the removal of the thumbprint also reduces the overall token size.

## Conversion to JWK Thumbprints

JWK thumbprints and JWK URI are defined to retain the same required member names and values. As such, A single JWK URI can be converted into a single JWK Thumbprint. This is done by first converting to a JWK as a JSON Object, and then generating a thumbprint from that object.

Although a single JWK thumbprint should correspond to a single JWK URI, the hashing function for generating thumbprints precludes conversion in the reverse direction.

# Security Considerations

Ellaborate: No limiting attributes, must be determined by context
  * "use"
  * "key_ops"
  * "alg"

Not useful for direct usage (e.g. transferring as signed message)

Ellaborate: No trust mechanism
  * e.g. no "x5u" / "x5c" / "x5t"
  * Can use protected headers to convey some of this info

Ellaborate: No key rotation
  * rotation of keys changes the identifier
  * key rotation becomes a higher level requirement, such as tempriarily mapping two JWK URI to the same identity.

# IANA Considerations

Ellaborate: Register "jwk" uri

{backmatter}

# Acknowledgments
{:numbered="false"}
