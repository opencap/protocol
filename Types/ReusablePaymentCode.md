# [WIP] Reusable Payment Code

A Reusable Payment Code Type holds a [BIP-47 payment code](https://github.com/bitcoin/bips/blob/master/bip-0047.mediawiki).  

| Type | Description              | Length  |
| 0    | Version 1 Payment Code   | 79 byte |
| 1    | Version 2 Payment Code   | 79 byte |

The base64-encoded address field contains [byte 1-79](https://github.com/bitcoin/bips/blob/master/bip-0047.mediawiki#binary-serialization) of the payment code (byte 0 is represented by the 'type' property).

Example:  
```javascript
{
    "type": 0,
    "address": "AAOwIM8B8m2MSCVGxqGa4lv/xjZaKS7TNyrfRtn3ZDicJVsILElv99gFQD7vTQNtA4QCYQuAKVEKas/IIJfud6xlAAAAAAAAAAAAAAAAAA=="
}
```
This represents a Version 1 Reusable Payment Code, namely PM8TJfFccT8JNYN6fWypnkHuWUeH1kyoZzri9qi8gtPajiJmP8TKJvfTzXVry9WWFU6bVuXyhjKJWurFdsZaHN294inAJ1JaSFzP9eEtfS1MQd1BDFda.
