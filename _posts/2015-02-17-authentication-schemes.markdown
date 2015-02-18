---
layout: post
title:  "Stateful vs. Stateless Authentication"
date:   2015-02-17 10:00:45
categories:
  - technical
---

We've been exploring the future of our authentication scheme and thus I've been
doing a lot of thinking and research on various ways to implement
authentication.  A few concepts have bubbled out of this research that I think
are interesting and worth exploring.
[Link to Specification][http-signature]

# Categories of Authentication

The schemes that I've looked at can all be categorized into one of the
following two categories.


## Stateful Authentication

From the perspective of the **server**, a stateful authentication system is one
that uses a persistance layer such as a database to store authentication state
as well as any metadata about expiration times or revokation of tokent.
Standard session/cookie based authentication typically falls into this
category.

From the perspective of the **client**, a stateful authentication system is one
in which the possession of a valid token grants the possessor authentication
with the server.  Standard session/cookie based authentication is an example of
this, as anyone who knows the value of a client's session cookie can
authenticate with the server as the client.

## Stateless Authentication

From the perspective of the **server**, a stateless authentication system is
one that does not require a persistance layer.  The schemes that implement
stateless authentication tend to do so using cryptographic signatures.  The
simplest implementation of this would be for the server to provide the client
with a signed piece of data that included the client's identity.  The client
would then give that token back to the server on each request.  The server
merely checks the signature, and if valid, trusts that the client is who the
signed token says she is.

From the perspective of the **client**, a stateless authentication system is
one in which the client must actively perform some action beyond merely sending
along some static token in order to authenticate with the server.  [HTTP
Signature][http-signature] is on example of this.  The TLDR version of HTTP
Signature authentication is as follows.   The client has an identifier that can
be used by the server to look up the clients identity.  In addition the client
has a private piece of information that is also known by the server, but is
never transmitted to the server during authentication.  The client uses the
private information to sign some portion of the request as well as including
the public identifier.  The server can then use the public identifier to look
up the private piece of information, verify the signature, thus verifying the
identity of the client.


# Features

In order to evaluate differenc schemes, we've had to ask ourselves what our use
cases are, as well as what requirements we have for the scheme itself.  Our use
case is as follows.

- Authentication happens over the HTTP request/response cycle.
- Clients include.
  - A front end javascript application.
  - Authentication between internal services.
  - Authentication from 3rd party clients.
- Must have an expiration mechanism.
- Should have a revocation mechanism.
- Should protect against replay attacks.
- Should protect against hijacking authentication (such as intercepting a session key).

Additionally, we are looking at the following meta attributes for each scheme.

- How developed is the ecosystem.  Are there solid implementations and tooling
  in most common languages (and the languages that we use in house).
- Are other major entities using a similar scheme.
- Does the scheme impose any concerns around future scaling.

# Candidates

The following are only the candidates that I've looked into so they are by no
means meant to be an exhaustive list.

## Cookie/Session authentication

Client: Stateful
Server: Stateful

This scheme was disqualified due to it being a browser specific implementation
and we have use cases that involve server to server communication.  While using
a cookie based scheme is doable in those situations it is awkward to implement.

## API Token

Server: Stateful
Client: Stateful

This is the current scheme that we use for server to server communication.
While common and strait forward, it does not score very high on our checklist.

- Maintaining state on the backend is complex.
- The statefulness can pose scaling concerns.  Tokens must be retrieved on
  every request, as well as managing things like expiration.
- Provides no protection against replay attacks.
- Provides no protection against hijacking someone's authentication.

## HTTP Signature

[Link to Specification][http-signature]

Server: Stateful
Client: Stateless

> Disclaimer: This is my current preference so my ability to judge it
> impartially may be impaired.

The downsides to this scheme are that it still requires maintaining state.  The
major benefits of this scheme are that it provides very robust protection
against replay attacks and hijacking of authentication.

## JSON Web Tokens

[Link to Specification][jwt-specification]

In this scheme, the server would sign a packet of information that contained
the client's identity.  This token would then be given to the client.  To
authenticate, the client merely has to give this token back to the server on
each request.  From the server's perspective, verification of authentication is
just checking if the signature is valid, and then trusting the signed
information.  It is eligant and simple.

The drawbacks are that this scheme provides no protection against replay
attacks or hijacking authentication.  

There is also no way to revoke authentication once a client has been given a
valid JWT.  This can be mitigated by having a short expiration time, but that
too has it's drawbacks.


# Additional Thoughts.

## Expiration and Token Cycling.

Ideally, the tokens used for authentication are not long lived.  There may be
some exceptions to this, such as internal server to server communication.
Given that tokens will expire, there needs to either be a mechanism to extend
the life of a token or cycle tokens for a new token.  Also, a stateless server
scheme cannot extend the expiration of a token, so cycling is required in this
situation.

It's unclear to me where the responsibility for this should live.

If left up the the client, every client must both know about when it's token
will expire, as well as implementing a mechanism to get a new token.  It may
seem like this simplifies the server implementation, but I think that is false,
given that the server must provide a mechanism for the client to extend or
cycle an active token.

If left up to the server, there must be a mechanism in place to provide a new
token in the request response cycle.  For things like cookies, this is trivial,
but our use cases involve clients that are not web browsers.  It's unclear how
to implement a simple solution for this.

## Replay Attacks and Hijacking.

Assuming all traffic goes over HTTPS/SSL, I'm beginning to think that these two
concerns don't matter.  However, our infrastructure special cases traffic
within the network such that all outside traffic is over HTTPS, but all
communication between our servers is over plain HTTP.

## Stateless vs Revocation

These two seem to be mutually exclusive.  If a system has revocable tokens, it
seems like it must have a persistance layer in order to be able to look up and
subsequently reject a given token.  If a service is stateless, it has no
persistance layer, and thus, cannot know what tokens exist, nor can it revoke
an active token since it's signature will be valid until it naturally expires.


# Closing Thoughts

At this point in my career I know enough about crypto to know that I should not
be implementing my own crypto.  I have however had my own ideas for
authentication schemes that attempt to scrape up the best parts of various
schemes.


[jwt-specification]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[http-signature]: https://datatracker.ietf.org/doc/draft-cavage-http-signatures/
