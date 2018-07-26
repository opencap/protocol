# Bitcoin

A Bitcoin Type address either holds a public key hash or a script hash.  

| Type | Description             | Length  |
| ---- | -----------             | ------  |
| 0    | Public key hash (P2PKH) | 20 byte |
| 1    | Script hash (P2SH)      | 20 byte |

Example:  
```javascript
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg="
}
```
This represents a P2PKH address, namely 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa.