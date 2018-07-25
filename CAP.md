# CAP - Crypto Alias Protocol

The CAP sub-protocol can be considered the only "required" portion of the opencap protocol. CAP allows a client (wallet) to be able to take an alias as input and derive the appropriate crypto address to send to. The following endpoint is all that is required of a CAP server:

## GET /{domain}/{username}/{ledger_id}

No Authorization (Public method)

Returns the address of the related domain, username and ledger, if it exists on the server.

### Example Usage

* Bob decides to send Alice 5 BCH via her alias alice@domain.tld
* Wallet finds a CAP server running at https://domain.tld:41145/
* Ledger ID for Bitcoin Cash = 0
* alice@domain.tld -> GET <https://domain.tld:41145/domain.tld/alice/0>
* If Alice truly has a Bitcoin Cash address, the address will be sent back to Bob's wallet with an HTTP 200 Status OK.

```javascript
Code: 200
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg=",
    "extensions": {
        "name": "Satoshi Nakamoto",
        "dns_sig": "..."
        "pgp_sig": "..."
    }
}
```

* Bob's wallet can notify Bob that a valid address was found, and the wallet can now route a payment to Alice through her address. Furthermore, it's recommended to check attached signatures if any for improved security.

A wallet that uses multiple "ledgers" (Bitcoin has legacy addresses and segwit addresses that constitute different ledgers within CAP) would first query the endpoint using the ledger_id of the preferred ledger, and if that fails it can try other ledgers. If the sender was able to send to a segwit address, they would first query the Bitcoin SegWit Ledger ID and if that fails, they would try the Bitcoin Legacy Ledger ID.