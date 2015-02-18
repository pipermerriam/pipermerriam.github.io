---
layout: post
title:  "Bad Ideas - AKA Implementing my own crypto"
date:   2015-02-17 10:00:45
categories:
  - technical
---

In the course of my exploration of various authentication schemes, I've
inevitably had my own ideas.

# Goals

I'd ideally like to achieve all of the following to some degree.

## Fault tolerant

Services **should** be able to function for at least some short amount of time (30
minutes?) in the event of something catestrophic such as a network partition,
or the authentication service going completely down.

## Scalability

It **must** be capable of scaling to millions of concurrently connected users.

## Expiration

This one is easy, but it **must** provide an expiration mechanism.

## Revocation

It **should** in most normal cases allow for revocation of authentication.

## Replay Attack Protection

It **should** protect against replay attacks in most situations.

## Hijacking of Authentication

It **should** protect against having your authentication hijacked.  In our
case, we have some traffic which does not go over HTTPS/SSL, so I believe this
is a legitimate concern.

## Transmission of Sensitive Data

This is tangentially related to dealing with replay attacks and hijacking
authentication, but, it **should** involve little or ideally no transmission of
sensitive data.  For example, sending a JWT to the server is effectively
sending all of the information someone needs to authenticate as the client for
as long as the token is valid.

# The Bad Idea

My idea is basically a hybrid of HTTP Signature and JSON Web Token (JWT).

There would be an authentication service which maintains a
public-key/private-key/identity mapping in a database.  Expiration, Revocation,
and Token Extension can all be backed via the database.
