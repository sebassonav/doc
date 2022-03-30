# Concepts

The following page briefly describes basic concepts and glossary commonly referred to when working with OAuth 2.0 and
OpenID Connect.

Most of the documentation later on assumes that you are familiar with these concepts, so take your time to read through
this page (or come back when you stumble upon something you're unfamiliar with).

## Identity Provider

An _identity provider_ (IdP) is a common service that NAV delegates to for authentication of end-users and/or services.

For example:

OpenID Connect:

- [Azure AD](azure-ad/README.md)
- [ID-porten](idporten/README.md)

OAuth 2.0:

- [Azure AD](azure-ad/README.md)
- [Maskinporten](maskinporten/README.md)
- [TokenX](tokenx.md)

## JWT

JSON Web Token (JWT) is a compact, self-contained means of representing claims as a JSON object to be transferred
between two parties, as specified in [RFC 7519](https://tools.ietf.org/html/rfc7519).

The contained claims can be verified and trusted by validating its digital signature. In most cases, the JWT is signed
using an asymmetric secret (i.e. public and private key pair), where the signer will sign the JWT using their private
key. The receiving party may verify the signature using the signer's associated public key, which may be distributed
out-of-band or [published at a well-known endpoint](#jwks-endpoint-public-keys).

### Contents

In short, a serialized JWT will look something like this in its compact form (where each part is base64-URL-encoded):

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

The token consists of three parts:

```
<header>.<payload>.<signature>
```

### Header

The _header_ consists of metadata describing the token, such as:

* `kid`: The key ID of the associated key used to sign the token.
* `typ`: The type of token, usually `JWT`.
* `alg`: The algorithm used to sign the token, usually `RS256`.

Example:

```json
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "<some-key-id>"
}
```

### Payload

The payload contains _claims_ about the associated user or principal that the token represents as well as additional
data.

JWTs will mostly consist of [registered claims](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) or "standard"
claims. Depending on the identity provider however, additional claims may be included.

Example (with registered claims only):

```json
{
  "iss": "https://identity-provider.no",
  "sub": "f6V1gB18pwpV06nWjbPLMxdjZiefEnl7G5oNih6LDUI=",
  "aud": "302fc528-9d76-481a-bd4f-92a540d738c8",
  "nbf": 1648645430,
  "iat": 1648645430,
  "exp": 1648649030,
  "jti": "W1bYOUA0WT1rEYUjE2YfXge0cfVSgiE5sSwIMM4tSSA"
}
```

See [RFC 7519, section 4.1](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) for descriptions of these claims.

Parties receiving JWTs should validate the claims found in the payload. See [token validation](#token-validation).

### Signature

While the RFC does not require a JWT to contain a signature, a JWT will quickly lose its value if it does not have a
signature at all. The signature is used to verify the integrity of the information contained within the token. Any
recipient of the JWT can verify that the token, nor its contents, has been tampered with or modified at any point after
being issued by the issuer.

Signatures are created by using the header and payload as input. You should generally not have to implement this
yourselves and rather rely on compliant libraries to do so for you.

### Further reading

Interested readers may find a more thorough introduction here:

- <https://jwt.io/introduction>

And, of course (for the adventurous), the RFC itself:

- [RFC 7519](https://tools.ietf.org/html/rfc7519)

## JWK

[JSON Web Key](https://datatracker.ietf.org/doc/html/rfc7517)

### Private Keys

If your application integrates with one of the identity providers in use at NAV, you will generally have a unique
_private key_ for each combination of (client, identity provider).

The private key should be, as its name implies, _private_. It is considered to be a secret in line with passwords and
other sensitive keys. It should _never_ be exposed to the browser, an end-user, or committed to version control.

The purpose of this private key is to be used in [client assertions](#client-assertion) where your client authenticates
itself with a signed JWT.

### Public Keys

Every private key can have a _public_ key that can be freely distributed to other parties. The main purpose of these
public keys is to _verify_ signatures attached to payloads (such as JWTs) that were signed using the associated private
key.

In most cases as developers, the only public keys you need to concern yourselves with are
the [public keys belonging to the identity provider](#jwks-endpoint-public-keys) you're using. These are needed
to [verify the signatures](#signature-validation) of the tokens that are signed and issued by the identity provider.

Conversely, when using [client assertions](#client-assertion), we will have pre-registered corresponding _public_ keys
associated with your client's _private_ key at the identity provider. The identity provider will use these public keys
to verify the signature of your client assertions when your client [authenticates itself](#client-assertion) with the
identity provider.

## Well-Known URL

Also referred to as the Discovery Endpoint or Metadata Document Endpoint or other similar names. The _well-known URL_

### Issuer Endpoint

### Token Endpoint

### JWKS Endpoint (Public Keys)

## Tokens

A token is something. A [JWT](#jwt) is a token, but not all tokens are JWTs.

### ID Token

### Access Token

### Refresh Token

## Token Validation

### Signature Validation

### Claims Validation

### Best Practices

## Client Authentication

### Client ID

### Client Secret

### Client Assertion


