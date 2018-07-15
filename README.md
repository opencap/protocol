# CCAP - CryptoCurrency Alias Protocol

Inception: July 2018

## Contents

[Problem](#problem)

[Solution](#solution)

[Format of this Repository](#format-of-this-repository)

[Anatomy of an Alias](#anatomy-of-an-alias)

[Security](#security)

[Required Endpoints](#required-endpoints)

[Optional Endpoints](#optional-endpoints)

[Attack Vectors](#attack-vectors)

[Contribute](#contribute)

[CCAP Compliant Software](#ccap-compliant-software)

## Problem

Sending cryptocurrency is not as easy as sending fiat currently. Centralized apps like Venmo, Swish, or Square Cash are more convenient and have less room for error in that they allow users to send money via usernames, emails, or phone numbers which are easy to remember and verify. Cryptocurrency addresses by nature are hard to remember, and when typed manually can easily contain errors.

Cryptocurrency addresses also change over time for a given user in some currencies (wallets use new addresses for each transaction, or users create new wallets). When posting addresses online, future payments will use the same address which defeats the purpose of the non-reuse feature. An alias relates to an address that can be updated, which solves this problem.

## Solution

CCAP is a protocol that defines the standard by which cryptocurrency wallets can communicate with servers to relate aliases to cryptocurrency addresses. The protocol allows for the decentralization of the alias --> address relationship. Anyone can use the protocol to build/host their own server on their own domain. Servers can be majorly centralized, or individuals can run their own servers, much like SMTP (email).

CCAP is adhered to by both client (wallet) software and server (address storage) sofware. Wallets must know how to properly parse an alias and perform the proper API calls. Servers must know how to handle those requests in the proper manner and send back valid data. Any given wallet and server that adhere to the CCAP protocol will be able to communicate cryptocurrency address information seamlessly.

CCAP is a simple protocol that does one thing: relate aliases to addresses. Servers can of course add more features to their servers (profile pictures, address re-use and validation, etc.) but these features are not a part of the base protocol.

## Format of this Repository

The CCAP project is completely open source. This root README.md file describes the protocol in general as it applies to all coins. Each coin also has it's own specific folder that contains additional protocols. The subdirectories can have additional requirements or exceptions. The protocols in a coins subdirectory will take priority over the root protocol in case of a conflict.

Subdirectory README files should follow the same general format as this root file.

## Anatomy of an Alias

### @{username}.{domain name}

### example: @lane.ogdolo.com

The only information necessary in the alias is the domain where a user's address information is stored and a username. The @ symbol in front is to not confuse aliases with email addresses. Username's cannot contain a "." because this can cause ambiguity when subdomains are used. For example @lane.crypto.ogdolo.com. It is not clear if this means lane.crypto is the username, or if crypto.ogdolo.com is the domain name.

Note: There is current discussion/debate around the format of the alias. If you have an opionion please express it in the appropriate issue that has been raised.

## Security

CCAP does does not define the standard by which data should be stored and encrypted on either the client or server side. Those are details that depend upon the specific implementation. CCAP is only a communication protocol. Naturally, CCAP defines the security of communication (HTTPS) and the client-->server authentication endpoints.

While security isn't enforced explicitly, each README in this repo will have a section marked "Attack Vectors" which describes where known possible security holes lie. In that section solutions will be discussed and provided so that server and client devs can know how to protect themselves and their users.

## Required Endpoints

### POST /ccap/address

Authorization: Bearer {jwt}

Adds or updates the address for the authenticated user of the given coin type.

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| coin | The ticker symbol of the coin for this address | Yes | "BTC"
| address | The first valid recieving address for this wallet | Yes | bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp

#### Example Usage

* Alice recieves a payment from Bob and her wallet recognizes that a payment was recieved
* Assuming Alice wants to update her address, her wallet will generate a new address
* Alice's wallet uses her alias to construct a URL and HTTP Method
* @alice.ogdolo.com -> POST <https://ogdolo.com/ccap/address>
* Alice's wallet authenticates with the CCAP server using her credentials to get a JWT that will securely allow her wallet to update her address
* Alice's wallet constructs the JSON request to send to the server

```javascript
Authorization: BEARER {jwt}
{
    "coin": "BTC",
    "address": "bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp",
}
```

* Alice sends the request and her address is updated on the server

### GET /ccap/address/{username}/{coin}

No Authorization (Public method)

Returns the address of the related username and coin, if they exist.

#### Example Usage

* Bob's decides to send Alice 5 BTC via her alias @alice.ogdolo.com
* Bob's wallet parses the alias into a URL destination and HTTP method
* @alice.ogdolo.com -> GET <https://ogdolo.com/ccap/address/alice/btc>
* If alice truly has a Bitcoin address hosted on the domain that her alias suggests, the address will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "address": "bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp",
}
```

* Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address.

### POST /ccap/auth

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

## Optional Endpoints

The following endpoints are not REQUIRED to adhere to CCAP, because most of those functions could also be handled by a webpage portal, or simply on the server backend side if the user is running their own server. However, IF a server or wallet are going to implement the functionality of the following endpoints, they should do it in this manner. If for some reason this is impossible, please submit an issue/PR so we can fix the problem.

### POST /ccap/user

No Authorization (Public method)

Create a new user on the server.

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| username | The username of the user | Yes | "lane"
| password | The password of the user | Yes | "SU93Rc00lpa55"
| email | The email of the user | Yes | "lane.wagner@gmail.com"
| ... | If additional information is required for sign-ups, then this endpoint shouldn't be used. Clients should sign up via a custom front-end portal of some sort. | ... | ...

#### Example Response

```javascript
Code: 200
{
    "message": "success"
}
```

### DELETE /ccap/user

Authorization: BEARER {jwt}

Delete all records of the authenticated user on the server.

#### Example Response

```javascript
Code: 200
{
    "message": "success"
}
```

### DELETE /ccap/address/{coin}

Authorization: BEARER {jwt}

Delete the address of the specified coin for the authenticated user.

#### Example Response

```javascript
Code: 200
{
    "message": "success"
}
```

## Attack Vectors

### Address Swaps

The most obvious way for a malicious party to use the alias system to steal funds would be to hack a CCAP server and secretly change out addresses so that their address is associated with someone else's alias.

Possible remedies:

* Allow users to turn on 2FA for address updates
* Only allow address updates from verified IP addresses
* Run a CCAP server yourself instead of using a third party (we anticipate the majority of users will prefer a third party service, like gmail for SMTP for example, but this is not required by the protocol)
* Only allow address updates manually through a browser. Some currencies may not update addresses often, if ever. (Nano for example)
* Use 2 way encryption like AES-256 to encrypt and store aliases with passwords. Only unencrypt when necessary.

### Payment Tracking

It is fairly simple for a third party to constatly poll a given aliases endpoint and record all the addresses as they are server over time. This is a breach of privacy. While the alias protocol isn't necessarily meant to have stringent privacy measures (the whole point of an alias is to relate an account to an address) there are a couple things that can be done to increase privacy:

* Coins that are able to implement features similar to BIP 47 should do so and use payment codes instead of regular addresses.
* Servers can have sign-ups that don't require any personal information, just an anonymous alias.

## Contribute

CCAP is an open-source protocol please feel free to submit change proposals via the "issues" tab on github, or by submitting a pull request.

## CCAP Compliant Software

* Nothing here yet... we just released! Give us a sec ;)
