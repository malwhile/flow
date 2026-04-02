---
author: "Halvo (Human)"
title: "Exploring Enrollment over Secure Transport"
date: 2023-03-30
tags:
  - est
  - secure-transport
  - certificate-pinning
  - public-key
  - tls
  - authentication
  - security
draft: false
summary: |
  A light‑hearted dive into RFC 7030 (EST) as a smarter alternative to hard‑coded, pinned certificates. By letting clients fetch fresh TLS certs via a public‑key‑authenticated EST server, you dodge the nightly release‑cycle nightmare, gain easy revocation, and keep the private key out of the binary—plus a dash of extra work for the user that’s worth the security payoff.
---

## Introduction

Currently one of my projects uses "pinned" certs to securely communicate back to a REST service. These are pinned to allow for truly secure authentication of the server, eliminating a rogue certificate authority (CA) to issue a fake cert and allow for man-in-the-middle (MITM) attacks. This is a huge hassle as the server and client need to stay in sync. This involves cutting a new release just to update certs and trying to get them deployed in the expiration/reissue window. [Enrollment over Secure Transport](https://www.rfc-editor.org/rfc/rfc7030.html) (EST) should provide a better way to issue certs from the server so the client just has to request the new ones and download them.

## What is EST

EST allows a client to authenticate to the EST server, which then delivers a client cert. This could be unique to the client or generic for all clients. Issued certificates can then be used to re-authenticate to the EST to get the updated cert. By having this re-authentication method, a client can automatically get the most up-to-date cert in a secure way. By not having it compiled into the binary (i.e. pinning) a new release is not needed to simply update the cert.

To do this, the client authenticates to the EST server, either via public/private key pair or username/password, and the client authenticates the server, either through the same public/private key challenge or external CA. Once authenticated, the EST server will issue the correct cert. All communication is over a TLS connection.

## Possible Setup

First, no to username/password. With username/password authentication, the client will be reliant on an external CA to authorize the server, which is what "pinning" was supposed remove. So, if username/password is used, there is no real need for an EST server and the client can just connect directly to the server (for our use case).

So using pub/priv key pair seems to be the best way to move forward. The EST server will have public keys for each client that will be connecting, the clients in turn will either have their key built into the binary or distributed offline. These two methods are almost pure foils of each other, so a pro for one is a con for the other. So let's just look at my preference (spoiler alert) having the client keep track of their key.

Pros of giving the client their key to keep track of:

- Keeping the key separate makes it easier to revoke if compromised, just give them a new key and have them restart their software
- By keeping it separate it should also mean that it'll be less likely to become compromised, since reverse engineering the binary won't be an issue
- Once initial connection is established the key can be deleted and it's no longer on the client's machine

Cons of a separate key

- The client must keep track of the key, they could accidentally delete it and need it re-issued
- It adds additional steps to installation as the key needs to be in place for anything to work

Being able to easily revoke and re-issue a private key is the deciding factor for me. This is the true solve to the problem of pinning. Building in the private key helps with the pinning issue as it doesn't need to be updated as frequently, but it really just delays the issue. Yes it's more work for the client to get everything setup, but a little inconvenience shouldn't get in the way of good security.

## Final Proposal

The final setup could look something like this:

1. Pub/Priv key pair is generated
1. Pub key is put onto the EST which maintains a database of pub key to TLS cert relationships
1. Pub/Piv key pair is given to the client to be put in place upon software installation

Once the software is installed it would:

1. Pull in the priv key
1. Connect to the EST
1. Establish trust through an encrypted challenge response
1. EST issues a unique TLS cert to that client
1. Client uses TLS cert to connect and authenticate to backend server
1. When TLS cert expires, it can be used to re-auth with the EST and download the next TLS cert

## Conclusion

Using this method of authentication with a pub/priv key pair to an EST, then using the issued TLS cert for authentication is the best way to remove the need for pinned certificates and username/passwords. The private key is the primary way the client authenticates, since it uses that key pair to get the TLS cert. Using the TLS cert for authentication makes it so a client doesn't need to continuously update passwords. By having the private key separate from the binary, and the TLS cert for authentication, it becomes relatively simple to re-issue creds when a system is compromised.

