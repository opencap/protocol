# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send a payment to. There are only two things required of a server to be CAP compliant:

## 1.
The domain of the CAP server must have a SRV record that points at itself. This is so that the other sub-protocol ([CAPP](/CAPP.md)) has the possibiliy of allowing proxy aliases. For more info on that see CAPP.md in the root of this repository. The format of the SRV record is as follows:

```javascript
CAPP-opencap.domain.tld:443
```

Where "opencap.domain.tld" is the domain where the CAP server is running and 443 is the port it is listening on. Any subdomain can be used but "opencap" is standard. Any port may be used but communication is required to be over HTTPS.

## 2.
The following endpoint must be supported:

## GET address/{asset}/{username}/{domain}

No Authorization (Public method)

Returns the address of the related domain, username, and ledger if it exists on the server.

### Example Usage

* Bob decides to send Alice 5 BTC via her alias alice@domain.tld
* Bob's wallet does a name server lookup to the SRV record for domain.tld
* Wallet finds a SRV record that contains:

```javascript
opencap.domain.tld:443
```

which specifies that this alias is not a "proxied" alias and is hosted on the server with the same domain name as that of the alias.

* Wallet now knows that a CAP server is running at https://opencap.domain.tld:443
* Bob is sending BTC and the Asset ID for BTC is 0
* Bob's wallet makes a GET request to the formulated URL: https://opencap.domain.tld:443/0/alice/domain.tld
* If Alice truly has a BTC address on the server it will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "addresses":[
        "type": "legacy",
        "address": "1AsXFv29DNMLGctzhhwjp...",
        "extensions": {
            "name": "My legacy address",
            // any other asset specific info can be in here
        },
        "type": "segwit",
        "address": "bc1qvw0ytfntx6zs0lfsruem...",
        "extensions": {
            "name": "My segwit address",
            // any other asset specific info can be in here
        }
    }
    ]
}
```

Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address of the type Bob wants to send to.

Some assets have multiple types. In these instances it is possible that an alias has mutliple addresses stored, one of each type. For this reason all available address types of the given asset are returned in an array. In the above example segwit addresses and legacy addresses have different types in regards to the BTC asset.

Each asset type has it's own file specifying the quirks of that asset, the names of the address types, etc.