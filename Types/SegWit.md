# [WIP] SegWit Types

A SegWit Type address either holds a public key hash or a script hash.  

| Type | Description              | Length  |
| ---- | -----------              | ------  |
| 0    | Public key hash (P2WPKH) | 20 byte |
| 1    | Script hash (P2WSH)      | 32 byte |

Example:  
```javascript
{
    "type": 0,
    "address": "6N8BjH4ybMJT+qx+Rs3FHmhULEI="
}
```
This represents a P2WPKH address, namely bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq.
