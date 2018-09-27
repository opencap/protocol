# Address Query

The Address Query sub-protocol can be considered the only "required" portion of the opencap protocol. Address Query allows a wallet (client) to be able to take an alias as input and derive the appropriate crypto address from a server.

## Server Requirements

### 1. A SRV record on the alias's domain

The SRV points to the location where the host server's REST API is running.

The format of the SRV record is as follows:

```bash
_service._proto.name. TTL class SRV priority weight port target.
```

Where the following values are required:

| name    | value   |
| ------- | ------- |
| service | opencap |
| proto   | tcp     |
| class   | IN      |
| port    | 443     |

As an example:

```bash
_opencap._tcp.example.tld. 86400 IN SRV 5 12 443 cap.example.tld.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

It is ideal for the SRV record to be secured using DNSSEC to mitigate against man-in-the-middle attacks, but not required.

### 2. Host the following endpoint at the URL where the SRV points

#### GET /v1/addresses?alias={alias}&address_type={address_type}

No Authorization (Public method)

Returns the address(es) of the related domain and username. If the address_type parameter is included then only that address type is returned, otherwise an array containing all of the user's addresses is returned. The "extensions" field in the return value will contain optional superflous information about the address.

Each address_type has it's [own file](/AddressTypes/README.md) in this repo specifying the details and ID number.

The endpoint must be served over HTTPS on port 443.

Request:

```javascript
HTTP/1.1
GET /v1/addresses?alias=alice$domain.tld&address_type=100
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
GET /v1/addresses?alias=alice$domain.tld
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

It is ideal for the DNS record for the domain of the REST API be secured using DNSSEC to mitigate against man-in-the-middle attacks, but not required.

## Wallet (Client) Requirements

When a wallet wants to find an address for a given alias first it looks up the SRV record for the domain. If the alias is "username$example.tld" then this would be done on linux in the following way:

```bash
dig SRV _opencap._tcp.example.tld
```

There are many [tools](/Tools.md) that can make this lookup simple using different frameworks and languages.

The format of the SRV record returned is as follows:

```bash
_service._proto.name. TTL class SRV priority weight port target.
```

For example:

```bash
_opencap._tcp.example.tld. 86400 IN SRV 0 5 443 cap.example.tld.
```

For more information on SRV records view: https://en.wikipedia.org/wiki/SRV_record#Record_format

Once the URL of the host server is found, in this example "cap.example.tld", the following request can be made:

### GET https://cap.example.tld/v1/addresses?alias={alias}&address_type={address_type}

See above in the "Server Requirements" section for the return payload structure.

The payload can then be parsed and the user of the wallet can decide which address_type (currency type) they want to send to the recipient.
