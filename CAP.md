# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send a payment to. There are only two things required of a server to be CAP compliant:

## 1.
The domain of the CAP server must have a SRV record that points at itself. This is so that the other sub-protocol ([CAPP](/CAPP.md)) has the possibiliy of allowing proxy aliases. The format of the SRV record is as follows:

```javascript
CAPP-{domain}:{port}-host
```

"CAPP" specifies that this SRV record is to be used for the CAPP protocol.

{domain} is the domain where the server is hosted. For example, opencap.ogdolo.com Any subdomain can be used but "opencap" is standard.

{port} Any port may be used but communication is required to be over HTTPS. Standard is 443

"host" specifies that this is a host server and not a proxy domain used by ([CAPP](/CAPP.md)).

## 2.
The following endpoint must be supported:

## GET /v1/domains/{domain}/users/{username}/assets/{asset_id}

No Authorization (Public method)

Returns the address of the related domain, username, and asset if it exists on the server.

### Example Usage

* Bob decides to send Alice 5 NANO via her alias alice@domain.tld
* Bob's wallet does a name server lookup to the SRV record for domain.tld
* Wallet finds a SRV record that contains:

```javascript
CAPP-opencap.domain.tld:443-host
```

which specifies that this alias is not a "proxied" alias and is hosted on the server with the same domain name as that of the alias.

* Wallet now knows that a CAP server is running at https://opencap.domain.tld:443
* Bob is sending NANO and the Asset ID for NANO is 1
* Bob's wallet makes a GET request to the formulated URL: https://opencap.domain.tld:443/domain.tld/alice/1
* If Alice truly has at least one NANO address on the server, it will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "type": 1,
    "address": "XKdD14CTd6BNYerDzfkqFDilogkaqdbZpaYq6EqxuQ8=",
    "extensions": {
        "name": "Alice",
        // ...
    }
}
```

Bob's wallet notifies Bob that a NANO address has been found (xrb_1q79ahdr36uqn38p5tp5sqwkn73rnpj1k8obtuetdbjcx37d5gahhd1u9cuh in this example). The wallet can now route a payment to Alice through her address.

Each asset has it's own file specifying the quirks of that asset, the names of the address types, etc. Those details are found here: [Assets](/Assets.md)