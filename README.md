# CCAP - CryptoCurrency Alias Protocol

<h2> Problem </h2>
<p>
Sending cryptocurrency is not as easy as sending fiat currently. Centralized apps like Venmo, Swish, or Square Cash are more safe and secure in that they allow users to send money via usernames, emails, or phone numbers which are easy to remember and verify. Cryptocurrency addresses by nature are hard to remember, and when typed manually can easily contain errors. 
</p>

<p>
Cryptocurrency addresses also change over time for a given user (wallets use new addresses for each transaction, or users create new wallets). An alias relates to an address that can be updated, which solves this problem.
</p>

<h2> Solution </h2>

<p>
CCAP is a protocol that defines the standard by which cryptocurrency wallets can communicate with servers to relate aliases to cryptocurrency addresses. The protocol allows for the decentralization of the alias --> address relationship. Anyone can use the protocol to build/host their own server on their own domain. Servers can be majorly centralized, or individuals can run their own servers, much like SMTP (email).
</p>

<p> CCAP is adhered to by both client (wallet) software and server (address storage) sofware. Wallets must know how to properly parse an alias and perform the proper API calls. Servers must know how to handle those requests in the proper manner and send back valid data. Any given wallet and server that adhere to the CCAP protocol will be able to communicate cryptocurrency address information seamlessly.</p>

<p> CCAP is a simple protocol that does one thing: relate aliases to addresses. Servers can of course add more features to their servers (profile pictures, address re-use and validation, etc.) but these features are not a part of the base protocol. <p>

<h2> Anatomy of an Alias </h2>
<h3 >@{username}.{domain name} </h3>
<h3> example: @lane.ogdolo.com </h3>
<p> The @ symbol is out in front to not confuse aliases with email addresses. </p>
<p> CCAP is a RESTful JSON protocol. The only information necessary in the alias is the domain where a user's address information is stored and a username.

<h2> Security </h2>
<p> CCAP does does not define the standard by which data should be stored and encrypted on either the client or server side. Those are details that depend upon the specific implementation. CCAP is only a communication protocol. Naturally, CCAP defines the security of communication (HTTPS) and the client->server authentication endpoints.
<p>

# Required Endpoints

<h2> POST /address </h2>

<p> Authorization: Bearer {jwt} </p>

<p>
Adds or updates the address for the authenticated user of the given coin type.
</p>

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| coin | The ticker symbol of the coin for this address | Yes | "BTC"
| address | The first valid recieving address for this wallet | Yes | bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp
| signature | The signature of the private key associated with this address. Proves validity. | Yes | "ADABIOB5234b35bol4b5QWEFSADFGARGAFGD"

<h5>Example Usage</h5>

* Alice recieves a payment from Bob and her wallet recognizes that a payment was recieved
* Assuming Alice wants to update her address, her wallet will generate a new address and associated signature
* Alice's wallet uses her alias to construct a URL and HTTP Method
* @alice.ogdolo.com -> POST https://ogdolo.com:703/address
* Alice's wallet authenticates with the CCAP server using her credentials to get a JWT that will securely allow her wallet to update her address
* Alice's wallet constructs the JSON request to send to the server
```
Authorization: BEARER {jwt}
{
    "coin": "BTC",
    "address": "bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp",
    "signature": "ADHIFDS54035fKIFDSI5904FAJPFNSFNS"
}
```
* Alice sends the request and her address is updated on the server


<h2> GET /address/{username}/{coin} </h2>

<p> No Authorization (Public method) </p>

<p>
Returns the address and signature of the related username and coin, if they exist.
</p>

<h5>Example Usage</h5>

* Bob's decides to send Alice 5 BTC via her alias @alice.ogdolo.com
* Bob's wallet parses the alias into a URL destination and HTTP method
* @alice.ogdolo.com -> GET https://ogdolo.com:703/address/alice/btc
* If alice truly has a Bitcoin address hosted on the domain that her alias suggests, the address and signature will be sent back to Bob's wallet with an HTTP 200 Status OK.

```
Code: 200
{
    "address": "bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp",
    "signature": "SDHAFIFSNADSIK54DSFIN76KNSDNF342AFPSDFSNDIF"
}
```
* Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address.


<h3> CREATE </h3>
<h4> POST /auth </h4>

<p> No Authorization (Public method) </p>

<p>
Gets a new JWT for the given user. The JWT can be used to authenticate with the other endpoints. JWT should last 5 minutes.
</p>

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| username | The username of the user | Yes | "lane.c.wagner"
| password | The password of the user | Yes | "SU93Rc00lpa55"

<h5>Example Response</h5>

```
Code: 200
{
    "jwt": "fn9s8dfasp7fub2uifb928rbpfdsfgasd9f8sdfbsa9fbsdf8ushdf9wbf"
}
```

# Optional Endpoints

<p> Of course servers are able to have other optional endpoints (user sign-ups, avatar pictures, password resets, etc) but those are not REQUIRED to adhere to CCAP, because most of those functions could also be handled by a webpage portal. CCAP simply facilitates alias -> address realtionships. </p>

<p> Some cryptocurrencies may have additional requirements, and we encourage them to build their requirements on top of this protcol to ensure maximal plug-and-play in the crypto industry </p>

# Special Thanks
I worked with the Nano Canoe dev team on this idea, and I'm sure other will help with the protocol before it is finalized.

