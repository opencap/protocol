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

All endpoints (Tier 1 and Tier 2) adhere to RESTful standards and communicate using JSON. 

All endpoints are served at the URL in the following format:

https://opencap.{domain}.{extension}:443/{endpoint}

HTTPS is required for all CAP endpoints, and as is standard the port 443 will be used.

Endpoints are prefixed with Protocol version in case of non-backwards compatibility.

<hr>

## TIER 1 ENDPOINT

The following GET endpoint is the only endpoint a server needs to support to be CAP 1 compliant. This is because in order for a user to have a CAP alias, wallets just need to know where to look up the address. CAP 1 can be as simple as a static string served from the endpoint.

### GET /v1/address/{ledger_id}/{username}

No Authorization (Public method)

Returns the address of the related username and ledger, if it exists on the server.

#### Example Usage

* Bob's decides to send Alice 5 BTC via her alias alice@ogdolo.com
* Bob's wallet parses the alias into a URL destination and HTTP method
* BTC_SEGWIT = ledger_id: 1
* alice@ogdolo.com -> GET <https://opencap.ogdolo.com/address/1/alice>
* If alice truly has a Bitcoin address hosted on the domain that her alias suggests, the address will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
    "extensions": {
        "dnssig": "..."
    }
}
```

* Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address.

A wallet that uses multiple "ledgers" (BTC has legacy addresses and segwit addresses that constitute different ledgers within CAP) would first query the endpoint using the ledger_id of the preferred ledger, and if that fails it can try other ledgers. If the sender was able to send to a segwit address, they would first query the BTC_SEGWIT id (1) and if that fails, they would try the BTC_LEGACY id (0).

<hr>

## TIER 2 ENDPOINTS

The following endpoints are required for a server to be CAP 2 compliant. These endpoints allow a wallet to know how to interact with a server on behalf of the user, in other words CREATE/UPDATE/DELETE address/user info.

### POST /v1/address

Authorization: Bearer {jwt}

Adds or updates the address for the authenticated user of the given coin type.

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| ledger_id | The ledger_id for this address | Yes | 1 (BTC_SEGWIT)
| address | The user's address for this ledger | Yes | YukHsVy/J9VCU5nr9vD7UOu4jxg=
| type | The address type | Yes | 0

#### Example Usage

* Alice recieves a payment from Bob and her wallet recognizes that a payment was recieved
* Assuming Alice wants to update her address, her wallet will generate a new address
* Alice's wallet uses her alias to construct a URL and HTTP Method
* alice@ogdolo.com -> POST <https://opencap.ogdolo.com/address>
* Alice's wallet authenticates with the CAP server using her credentials to get a JWT that will securely allow her wallet to update her address
* Alice's wallet constructs the JSON request to send to the server

```javascript
Authorization: Bearer {jwt}
{
    "ledger_id": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
}
```

* Alice sends the request and her address is updated on the server


### POST /v1/auth

No Authorization (Public method)

Gets a new JWT for the given user. The JWT can be used to authenticate with the other endpoints. JWT should last 5 minutes.

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| username | The username of the user | Yes | "lane"
| password | The password of the user | Yes | "SU93Rc00lpa55"

#### Example Response

```javascript
Code: 200
{
    "jwt": "fn9s8dfasp7fub2uifb928rbpfdsfgasd9f8sdfbsa9fbsdf8ushdf9wbf"
}
```

### DELETE /v1/user

Authorization: Bearer {jwt}

Delete all records of the authenticated user on the server.

#### Example Response

```javascript
Code: 200
{
    "message": "success"
}
```

### DELETE /v1/address/{ledger_id}

Authorization: Bearer {jwt}

Delete the address of the specified ledger for the authenticated user.

#### Example Response

```javascript
Code: 200
{
    "message": "success"
}
```

<hr>

## Contribute

CAP is an open-source protocol please feel free to submit change proposals via the "issues" tab on github, or by submitting a pull request. 

Build software that supports the protocol! The more wallets and servers actually work together to solve the alias problem, the easier it is to solve in the end. If you aren't a developer, reach out to your favorite project teams and let them know about the project!

<hr>

## CAP Compliant Software

* Nothing here yet... we just released! Give us a sec ;)
