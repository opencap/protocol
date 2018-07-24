# CAP - Crypto Alias Protocol

Inception: July 2018

Discord: https://discord.gg/A8Jkp2

<hr>

## Contents

[Problem](#problem)

[Solution](#solution)

[Format of this Repository](#format-of-this-repository)

[Anatomy of an Alias](#anatomy-of-an-alias)

[Security](#security)

[Attack Vectors](#attack-vectors)

[Justifications](#justifications)

[Endpoints](#endpoints)

[TIER 1 Endpoint](#tier-1-endpoint)

[TIER 2 Endpoints](#tier-2-endpoints)

[TIER 3 Endpoints](#tier-3-endpoints)

[Contribute](#contribute)

[CAP Compliant Software](#CAP-compliant-software)

<hr>

## Problem

Sending cryptocurrency is not as easy as sending fiat currently. Centralized apps like Venmo, Swish, or Square Cash are more convenient and have less room for error in that they allow users to send money via usernames, emails, or phone numbers which are easy to remember and verify. Cryptocurrency addresses by nature are hard to remember, and when typed manually can easily contain errors.

Cryptocurrency addresses also change over time for a given user in some currencies (wallets use new addresses for each transaction, or users create new wallets). When posting addresses online, future payments will use the same address which defeats the purpose of the non-reuse feature. An alias relates to an address that can be updated, which solves this problem.

<hr>

## Solution

CAP is a protocol that defines the standard by which cryptocurrency wallets can communicate with servers to relate aliases to cryptocurrency addresses. The protocol allows for the decentralization of the alias --> address relationship. Anyone can use the protocol to build/host their own server on their own domain. Servers can be majorly centralized, or individuals can run their own servers, much like SMTP (email).

CAP is adhered to by both client (wallet) software and server (address storage) sofware. Wallets must know how to properly parse an alias and perform the proper API calls. Servers must know how to handle those requests in the proper manner and send back valid data. Any given wallet and server that adhere to the CAP protocol will be able to communicate cryptocurrency address information seamlessly.

CAP is a simple protocol that does one thing: relate aliases to addresses. Servers can of course add more features to their servers (profile pictures, address re-use and validation, etc.) but these features are not a part of the base protocol.

<hr>

## Format of this Repository

The CAP project is completely open source. This root README.md file describes the protocol in general as it applies to all coins. Each coin also has it's own specific folder that contains additional protocols. The subdirectories can have additional requirements or exceptions. The protocols in a coins subdirectory will take priority over the root protocol in case of a conflict.

Subdirectory README files should follow the same general format as this root file.

<hr>

## Anatomy of an Alias

### {username}@{domain name}

### example: lane@ogdolo.com

Aliases have the same anatomy as an email address. This greatly benefits user experience as people are used to emailing, and payment services like PayPal allow sending to email addresses as well.  
Using email addresses does neither mean that a CAP service has to be run on the same host as the mail server nor that a mail server has to be run under the target domain name at all.

<hr>

## Security

CAP does does not define the standard by which data should be stored and encrypted on either the client or server side. Those are details that depend upon the specific implementation. CAP is only a communication protocol. Naturally, CAP defines the security of communication (HTTPS) and the client-->server authentication endpoints.

While security isn't enforced explicitly, each README in this repo will have a section marked "Attack Vectors" which describes where known possible security holes lie. In that section solutions will be discussed and provided so that server and client devs can know how to protect themselves and their users.

<hr>

## Attack Vectors

### Address Swaps

The most obvious way for a malicious party to use the alias system to steal funds would be to hack a CAP server and secretly change out addresses so that their address is associated with someone else's alias.

Possible remedies:

* Only allow address updates from verified IP addresses
* Run a CAP server yourself instead of using a third party (Using a third party isn't inherently insecure, and in the case of a non-programmer it is probably more secure)
* Only allow address updates manually through a browser and 2FA/Captcha. Some currencies may not update addresses often, if ever. (Nano for example)
* Use 2 way encryption like AES-256 to cipher address and user data before storing it in a database so it can't easily be swapped out

### Payment Tracking

It is fairly simple for a third party to constatly poll a given alias's endpoint and record all that alias's addresses over time. While the alias protocol isn't necessarily meant to have stringent privacy measures (the whole point of an alias is to relate a public account to an address) there are a couple things that can be done to increase privacy:

* Coins that are able to implement features similar to BIP 47 should do so and use payment codes instead of regular addresses.
* Servers can have sign-ups that don't require any personal information, so users can use an anonymous alias.

<hr>

## Justifications

* **Squatting:** CAP is a REST protocol built on top of DNS to stop "squatters". Squatting is when users that are the first-adopters of the protocol come in and steal all the valuable aliases ("nike", "coke", "trump", "btc", etc). By requiring that the a domain name is part of an alias, users have to first own the domain which is a system that is already fairly distributed.

* **TXT Records**: There is another protocol out there, OpenAlias, that proposes the usage of domain TXT Records for transmitting alias/address combinations. A lot of good work was done there, but we designed this system because we felt that most defvelopers are more comfortable building out a simple CRUD app with a database. If it is easier for developers to adhere tothe protocol, it will naturally be more distibuted and decentralized.

* **Why no blockchain?** The amount of data being stored on blockchains is a constant struggle, we are always trying to reduce the amount of data that needs to be verified by all nodes in a decentralized network (see the bitcoin scaling problem)Namecoin also never experienced great adoption so we saw no reason to beat that dead horse.

<hr>

## ENDPOINTS

All endpoints (Tier 1-3) adhere to RESTful standards and communicate using JSON. 

All endpoints are served at the URL in the following format:

https://opencap.{domain}.{extension}:443/{endpoint}

HTTPS is required for all CAP endpoints, and as is standard the port 443 will be used.

Endpoints are prefixed with Protocol version in case of non-backwards compatibility.

<hr>

## TIER 1 ENDPOINT

The following GET endpoint is the only endpoint a server needs to support to be CAP 1 compliant. This is because in order for a user to have a CAP alias, wallets just need to know where to look up the address. CAP 1 can be as simple as a static string served from the endpoint.

### GET /v1

No Authorization (Public method)

Returns general information about the server (name, software, supported ledgers, ...).

```javascript
Code: 200
{
    "name": "Example OpenCAP server",
    "software": "OpenCAP 1.0",
    "supported_ledgers": [
        0,
        1
    ],
    "donations": "donate@domain.tld",
    "registrations": true,
    "custom_domains": true
}
```

### GET /v1/users/{username}

No Authorization (Public method)

Returns some profile information about the user/alias.

```javascript
Code: 200
{
    "name": "Satoshi Nakamoto",
    "picture": "https://...",
    "pgp_fingerprint": "..."
}
```

### GET /v1/users/{username}/ledgers/{ledger_id}

No Authorization (Public method)

Returns the address of the related username and ledger, if it exists on the server.

#### Example Usage

* Bob decides to send Alice 5 BCH via her alias alice@domain.tld
* Bob's wallet parses the alias into a URL destination and HTTP method
* Ledger ID for Bitcoin Cash = 0
* alice@domain.tld -> GET <https://opencap.domain.tld/v1/users/alice/ledgers/0>
* If Alice truly has a Bitcoin Cash address hosted on the domain that her alias suggests, the address will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
    "dns_sig": "...",
    "pgp_sig": "..."
}
```

* Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address. Furthermore, it's recommended to check attached signatures if any for improved security.

A wallet that uses multiple "ledgers" (Bitcoin has legacy addresses and segwit addresses that constitute different ledgers within CAP) would first query the endpoint using the ledger_id of the preferred ledger, and if that fails it can try other ledgers. If the sender was able to send to a segwit address, they would first query the Bitcoin SegWit Ledger ID and if that fails, they would try the Bitcoin Legacy Ledger ID.

<hr>

## TIER 2 ENDPOINTS

The following endpoints are required for a server to be CAP 2 compliant. These endpoints allow a wallet to know how to interact with a server on behalf of the user, in other words CREATE/UPDATE/DELETE address/user info.

### POST /v1/users

No Authorization (Public method)

Creates a new user.

Body:
```javascript
{
    "username": "john",
    "password": "mysecretpassword"
}
```

Response:
```javascript
Status: 200
{

}
```

### DELETE /v1/users/:username

Basic Authentication

Deletes the user and all associated addresses.

Response:
```javascript
Status: 200
{

}
```

### PUT /v1/users/{username}/ledgers/{ledger_id}

Basic Authentication

Adds or updates the address for the user of the given ledger id.  
Please note that the authenticated user and the param in the url must match.

Body:
```javascript
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg="
}
```

Response:
```javascript
Status: 200
{

}
```

### DELETE /v1/users/{username}/ledgers/{ledger_id}

Basic Authentication

Deletes the address for the user of the given ledger id.  
Please note that the authenticated user and the param in the url must match.

Response:
```javascript
Status: 200
{

}
```

## TIER 3 ENDPOINTS

The following endpoints are required for a server to be CAP 3 compliant. These endpoints allow the desktop client to manage custom domains.

### POST /v1/domains/:domain

No Authorization (Public method)

Notifies the server that the DNS entries of 'opencap.' + specified domain point to it.  
- First, the server should check whether there actually is a CNAME record pointing to it.  
- Then, it reads the public key from the TXT record and saves it together with the domain name  
- At last, it asks Let's Encrypt to issue a certificate for 'opencap.' + specified domain  

Response:
```javascript
Status: 200
{

}
```

<hr>

## Contribute

CAP is an open-source protocol please feel free to submit change proposals via the "issues" tab on github, or by submitting a pull request. 

Build software that supports the protocol! The more wallets and servers actually work together to solve the alias problem, the easier it is to solve in the end. If you aren't a developer, reach out to your favorite project teams and let them know about the project!

<hr>

## CAP Compliant Software

* Nothing here yet... we just released! Give us a sec ;)
