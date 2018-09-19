# OpenCAP

Inception: July 2018

Discord: https://discord.gg/FTMCBE8

<hr>

## Contents

[Problem](#problem)

[Solution](#solution)

[Anatomy of an Alias](#anatomy-of-an-alias)

[Sub-Protocols](#sub-protocols)

[Text Standard](#text-standard)

[Attack Vectors](#attack-vectors)

[Justifications](#justifications)

[Contribute](#contribute)

[OpenCAP Compliant Software](#OpenCAP-compliant-software)

<hr>

## Problem

Sending cryptocurrency is not as easy as sending fiat currently. Centralized apps like Venmo, Swish, or Square Cash are more convenient and have less room for error in that they allow users to send money via usernames, emails, or phone numbers which are easy to remember and verify. Cryptocurrency addresses by nature are hard to remember, and when typed manually can easily contain errors.

Cryptocurrency addresses also change over time for a given user in some currencies (wallets use new addresses for each transaction, or users create new wallets). When posting addresses online, future payments will use the same address which defeats the purpose of the non-reuse feature. An alias relates to an address that can be updated, which solves this problem.

<hr>

## Solution

OpenCAP is a protocol that defines the standard by which cryptocurrency wallets can communicate with servers to relate aliases to cryptocurrency addresses. The protocol allows for the decentralization of the alias --> address relationship. Anyone can use the protocol to build/host their own server on their own domain. Servers can be majorly centralized, or individuals can run their own servers, much like SMTP (email).

OpenCAP is adhered to by both client (wallet) software and server (address storage) sofware. Wallets must know how to properly parse an alias and perform the proper API calls. Servers must know how to handle those requests in the proper manner and send back valid data. Any given wallet and server that adhere to the OpenCAP protocol will be able to communicate cryptocurrency address information seamlessly.

OpenCAP is a simple protocol that does one thing: relate aliases to addresses. Servers can of course add more features to their servers (profile pictures, address re-use and validation, etc.) but these features are not a part of the base protocol.

<hr>

## Anatomy of an Alias

### {username}${domain name}

### example: lane$ogdolo.com

The anatomy of an OpenCAP alias may remind you of an email address. The only difference being the seperator between username and domain. However it is very important to note that email addresses have nothing to do with OpenCAP aliases other than that they share a similar format.

The {username} section of the alias is only allowed to use:

1. Lower-case letters
2. Numbers
3. The following punctuation:
   - "."
   - "-"
   - "\_"

<hr>

## Sub-Protocols

OpenCAP is the generic name to describe the entire alias protocol. There are three sub-protocols that allow servers to decide to what level they wish to provide alias hosting.

They sub-protocol are listed in descending levels of importance. For example, all CAMP servers also must be CAP servers. All CAPP servers must also be CAMP and CAP servers.

1. [CAP](/CAP.md) - Crypto Alias Protcol: CAP is the base protocol and any server that wants to host aliases must at least adhere to CAP requirements.

2. [CAMP](/CAMP.md) - Crypto Alias Management Protocol: CAMP is a set of requirements that allow client (wallet) software to intereact with CAMP servers and make changes to the user's alias by CREATE/UPDATE/DELETE operations on the users account.

3. [CAPP](/CAPP.md) - Crypto Alias Proxy Protocol: CAPP describes how a user can set up a "proxy domain" so that all aliases using that domain can actually be served from a seperate host server while retaining the same domain name.

Each of these sub-protocols has its own file in the root of the repository describing the requirements.

<hr>

## Text Standard

All the text data transfer in the protocol can be assumed to be valid UTF-8 encoded.

<hr>

## Attack Vectors

### Address Swaps

The most obvious way for a malicious party to use the alias system to steal funds would be to hack a OpenCAP server and secretly change out addresses so that their address is associated with someone else's alias.

Possible remedies:

- Only allow address updates from verified IP addresses
- Run a OpenCAP server yourself instead of using a third party (Using a third party isn't inherently insecure, and in the case of a non-programmer it is probably more secure)
- Only allow address updates manually through a browser and 2FA/captcha. Some currencies may not update addresses often, if ever. (Nano for example)
- Use 2 way encryption like AES-256 to cipher address and user data before storing it in a database so it can't easily be swapped out

### Payment Tracking

It is fairly simple for a third party to constatly poll a given alias's endpoint and record all that alias's addresses over time. While the alias protocol isn't necessarily meant to have stringent privacy measures (the whole point of an alias is to relate a public account to an address) there are a couple things that can be done to increase privacy:

- Coins that are able to implement features similar to BIP 47 should do so and use payment codes instead of regular addresses.
- Servers can have sign-ups that don't require any personal information, so users can use an anonymous alias.

### Cross-Site Scripting XSS

Like any javascript client, wallets and other client-side OpenCAP software can be vulnerable to XSS attacks. All OpenCAP software must take precautions to not allow any data returned by a potentially untrustworthy server to be injected in a script and ran.

Reference:

[Wikipedia Cross-Site Scripting](https://en.wikipedia.org/wiki/Cross-site_scripting#Preventive_measures)

### DNS Poisening and MITM attacks

Because we are using the DNS system for SRV record lookups and to make API calls, there is potential for some form of DNS attack. If not handled properly this kind of attack could result in stolen funds because a malicious user could redirect API calls to their own server with their own addresses. None of the following solutions are required by OpenCAP but are suggestions that individual software implementations can use to help
mitigate attacks.

#### Servers

- DNSSEC: Servers (both the actual host and the domain record host) can use DNSSEC to protect themselves

[Wikipedia DNSSEC](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions)

#### Wallets (clients)

- DNSSEC: verify that DNS responses use DNSSEC, and issue a warning to the user if they don't

- DNS over HTTPS can be used to bypass local issues

  - [Google DNS HTTPS API](https://dns.google.com/)

  - [Cloudflare DNS HTTPS API](https://developers.cloudflare.com/1.1.1.1/dns-over-https/json-format/)

- Hostname caching. For instance, whenever a wallet makes a SRV lookup to "example.tld" it should recieve the same domain name, let's say "opencap.example.tld". The wallet can cache this domain name and if it ever recieves a new name, it can alert the sender and warn them that something may be wrong.

#### Individuals

- DNSCrypt can secure your device from MITM attacks: https://dnscrypt.info/

<hr>

## Justifications

- **Squatting:** OpenCAP is a REST protocol built on top of DNS to stop "squatters". Squatting is when users that are the first-adopters of the protocol come in and steal all the valuable aliases ("nike", "coke", "trump", "btc", etc). By requiring that the a domain name is part of an alias, users have to first own the domain which is a system that is already fairly distributed.

- **TXT Records**: There is another protocol out there, OpenAlias, that proposes the usage of domain TXT Records for transmitting alias/address combinations. A lot of good work was done there, but we designed this system because we felt that most defvelopers are more comfortable building out a simple CRUD app with a database. If it is easier for developers to adhere tothe protocol, it will naturally be more distibuted and decentralized.

- **Why no blockchain?** The amount of data being stored on blockchains is a constant struggle, we are always trying to reduce the amount of data that needs to be verified by all nodes in a decentralized network (see the bitcoin scaling problem)Namecoin also never experienced great adoption so we saw no reason to beat that dead horse.

<hr>

## Contribute

OpenCAP is an open-source protocol please feel free to submit change proposals via the "issues" tab on github, or by submitting a pull request.

Build software that supports the protocol! The more wallets and servers actually work together to solve the alias problem, the easier it is to solve in the end. If you aren't a developer, reach out to your favorite project teams and let them know about the project!

<hr>

## OpenCAP Compliant Software

| Link                    | Type             |
| ----------------------- | ---------------- |
| https://www.ogdolo.com/ | CAP, CAMP server |
