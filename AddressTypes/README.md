# Address Types

Address types are added in the order that there are wallets that support the protocol.  
Create a GitHub issue and propose an address type to be added to the system.

ID numbers are separted in partitions of 100. This allows a single coin's addresses to have ID numbers that are close numerically even if more types are added later on. For instace, all address type IDs 100-199 are reserved for Bitcoin.

Some coins will use the same address type, but they will be given their own IDs because clients may want addresses for different coins even if the formats are the same. A good example of this would be "Bitcoin P2PKH" and "Bitcoin Cash P2PKH". Both use the address format "Bitcoin P2PKH" but they are assigned separate IDs so that addresses intended for different blockchains don't get mixed up.

| ID  | Address Type          | Description                                           |
| --- | --------------------- | ----------------------------------------------------- |
| 100 | Bitcoin P2PKH         | [Bitcoin P2PKH](Bitcoin_P2PKH.md)                     |
| 101 | Bitcoin P2SH          | [Bitcoin P2SH](Bitcoin_P2SH.md)                       |
| 102 | Bitcoin Bech32        | [Bitcoin Bech32 Segwit](Bitcoin_Bech32.md)            |
| 103 | Bitcoin Payment Code  | [Bitcoin Payment Code Bip 47](Bitcoin_PaymentCode.md) |
|     |                       |                                                       |
| 200 | Bitcoin Cash P2PKH    | [Bitcoin P2PKH](Bitcoin_P2PKH.md)                     |
| 201 | Bitcoin Cash P2SH     | [Bitcoin P2SH](Bitcoin_P2SH.md)                       |
| 202 | Bitcoin Cash CashAddr | [Bitcoin Cash CashAddr](Bitcoin_Cash_CashAddr.md)     |
|     |                       |                                                       |
| 300 | Nano / Raiblocks      | [Nano Standard](Nano_Standard.md)                     |
