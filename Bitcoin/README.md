# CCAP - Bitcoin Specific Protocols

## BIP47 Support

<https://github.com/bitcoin/bips/blob/master/bip-0047.mediawiki>

BIP 47 solves some of the inherent privacy issues that CCAP has when simply tying aliases to addresses. According to BIP 47, a "payment code" can be used by the sender's wallet to generate new addresses for each payment.

When using a payment code instead of a regular address it is now impossible for a snooping third party to continously poll a users /ccap/address/{username}/BTC endpoint to keep a list of all of their addresses.

When a wallet parses an alias to make a payment, it should first make a request to the server at the payment code endpoint. If no payment code is retrieved, then the address endpoint is used.

## BIP 47 Endpoints

### POST /ccap/code

Authorization: Bearer {jwt}

Adds or updates the address for the authenticated user of the given coin type.

| Parameters | Description | Required | Sample Value |
| ---------- | ----------- | -------- | ------------ |
| coin | The ticker symbol of the coin for this address | Yes | "BTC"
| code | The payment code for the owner of this alias | Yes | bc1qvw0ytfntx6zs0lfsruem6xwj0mewng523ktatp

### GET /ccap/code/{username}/{coin}

No Authorization (Public method)

Returns the payment code of the related username and coin, if they exist.