# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send a payment to. I order to run a CAP server, that server must be configured and running at an address that follows this format:

```
https://opencap.{domain}.{ext}:443
```

All traffic is required to be served over HTTPS on port 443. {domain} and {ext} represent the domain and extension of the aliases that will be served from the server (with the exception of aliases adhering to the optional [CAPP](/CAPP.md) protocol). For instance if I want a server to host my alias "lane@ogdolo.com", I would run my server at:

```
https://opencap.ogdolo.com:443
```

This server must serve a single endpoint:

## GET /v1/addresses?alias={alias}&address_type={address_type}

No Authorization (Public method)

Returns the address(es) of the related domain and username. If the address_type parameter is included then only that address type is returned, otherwise an array containing all of the user's addresses is returned. The "extensions" field in the return value will contain optional superflous information about the address.

Each address type has it's own file in this repo specifying the quirks of that addresstype, the names of the address types, etc. Those details are found here: [Address Types](AddressTypes/README.md)

Request:

```javascript
HTTP/1.1
GET /v1/addresses?alias=alice@domain.tld&address_type=100
```

Response:

```javascript
HTTP/1.1
200 OK
Content-Type: application/json
{
    "address_type": 100,
    "address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
    "extensions": {
        "name": "Alice",
        // ...
    }
}
```

or

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
        "address_type": 100,
        "address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
        "extensions": {
            "name": "Alice",
            // ...
        }
    },
    {
        "address_type": 101,
        "address": "3J98t1WpEZ73CNmQviecrnyiWrnqRhWNLy",
        "extensions": {
            "name": "Alice",
            // ...
        }
    }
]
```

### Example Usage

- Bob decides to send Alice 5 NANO via her alias alice@domain.tld
- Wallet now knows that a CAP server is running at https://opencap.domain.tld:443, or that their is a CNAME record that will redirect to where the host is running.
- Bob is sending Nano and the Address Type ID for NANO is 300
- Bob's wallet makes a GET request to the formulated URL: https://opencap.domain.tld:443/v1/addresses/?alias=alice@domain.tld&address_type=300
- If Alice truly has a Nano address on the server, it will be sent back to Bob's wallet with an HTTP 200 Status OK.
- Bob's wallet notifies Bob that a Nano address has been found. The wallet can now route a payment to Alice through her address.
