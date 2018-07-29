# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send a payment to. There are only two things required of a server to be CAP compliant:

## 1.
The domain of the CAP server must have a SRV record that to where exactly the CAP application endpoints are being served. This is so that subdomains and third party servers can be used. For information on third party servers see ([CAPP](/CAPP.md)). 

The format of the SRV record is as follows:

```javascript
_service._proto.name. TTL class SRV priority weight port target.
```

Here the SRV record for Ogdolo.com to use as a reference:

```javascript
_cap._tcp.ogdolo.com. 86400 IN SRV 0 5 443 opencap.ogdolo.com.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

## 2.
The following endpoint must be supported:

## GET /v1/domains/{domain}/users/{username}/assets/{asset_id}

No Authorization (Public method)

Returns the address of the related domain, username, and asset if it exists on the server.

Request:

```javascript
HTTP/1.1
GET /v1/domains/{domain}/users/{username}/assets/{asset_id}
```

Response:

```javascript
HTTP/1.1
200 OK
Content-Type: application/json
{
    "type": 1,
    "address": "XKdD14CTd6BNYerDzfkqFDilogkaqdbZpaYq6EqxuQ8=",
    "extensions": {
        "name": "Alice",
        // ...
    }
}
```

Each asset has it's own file in this repo specifying the quirks of that asset, the names of the address types, etc. Those details are found here: [Assets](/Assets.md)

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
* Bob's wallet notifies Bob that a NANO address has been found (xrb_1q79ahdr36uqn38p5tp5sqwkn73rnpj1k8obtuetdbjcx37d5gahhd1u9cuh in this example). The wallet can now route a payment to Alice through her address.