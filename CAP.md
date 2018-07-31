# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send a payment to. There are only two things required of a server to be CAP compliant:

## 1. A SRV Record pointing to the host OR a host on the default domain and port

When a client looks up an address like "username@domain.tld", first it contacts "domain.tld" and checks for a SRV record with "_cap" as the service service. If it finds one, it will then contact the host specified in that SRV record for all API calls. IF there is no SRV record found, the client will instead try to make the API calls on the default host and port which in this case would be: https://domain.tld:41145

The SRV record allows CAP servers to use a separate domain/port if they choose to do so.

The format of the SRV record is as follows:

```
_service._proto.name. TTL class SRV priority weight port target.
```

As an example, Ogdolo hosts it's endpoints on the subdomain cap.ogdolo.com on port 443.
Here the SRV record for Ogdolo.com to use as a reference:

```
_cap._tcp.ogdolo.com. 86400 IN SRV 0 5 443 cap.ogdolo.com.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

## 2. The following endpoint must be supported

## GET /v1/addresses?alias={alias}&asset={asset}

No Authorization (Public method)

Returns the address of the related domain, username, and asset if it exists on the server.

Request:

```javascript
HTTP/1.1
GET /v1/addresses?alias=alice@domain.tld&asset=1
```

Response:

```javascript
HTTP/1.1
200 OK
Content-Type: application/json
{
    "type": 0,
    "asset": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
    "extensions": {
        "name": "Alice",
        // ...
    }
}
```

Each asset has it's own file in this repo specifying the quirks of that asset, the names of the address types, etc. Those details are found here: [Assets](/Assets.md)

If no asset is included in the query parameters, then this endpoint will serve a list of all assets for the given alias. For example:

Request:

```javascript
HTTP/1.1
GET /v1/addresses?alias=alice@domain.tld
```

Response:

```javascript
HTTP/1.1
200 OK
Content-Type: application/json
[
    {
        "type": 0,
        "asset": 0,
        "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
        "extensions": {
            "name": "Alice",
            // ...
        }
    },
    {
        "type": 0,
        "asset": 3,
        "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
        "extensions": {
            "name": "Alice",
            // ...
        }
    }
]
```

### Example Usage

* Bob decides to send Alice 5 NANO via her alias alice@domain.tld
* Bob's wallet does a name server lookup to the SRV record for domain.tld
* Wallet finds a SRV record that contains:

```javascript
CAPP-opencap.domain.tld:443-host
```

which specifies that this alias is not a "proxied" alias and is hosted on the server with the same domain name as that of the alias.

* Wallet now knows that a CAP server is running at https://opencap.domain.tld:443
* Bob is sending NANO and the Asset ID for NANO is 4
* Bob's wallet makes a GET request to the formulated URL: https://opencap.domain.tld:443/v1/addresses/?alias=alice@domain.tld&asset=4
* If Alice truly has at least one NANO address on the server, it will be sent back to Bob's wallet with an HTTP 200 Status OK.
* Bob's wallet notifies Bob that a NANO address has been found. The wallet can now route a payment to Alice through her address.