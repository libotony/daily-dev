---
title: Fee Delegation-VIP191
date: 2019-08-13 17:50:08
tags: [dapp,mpp,fee delegation,vip191,transaction fee]
---

In the previous article {% post_link Fee-Delegation-MPP-en %}, I introduced MPP, the `prototype-based transaction fee delegation protocol`. I also mentioned that it cannot meet the needs of developers in several circumstances. Noticeably, [Totient Labs](https://www.totientlabs.com/) combined multiple contracts in one transaction when developing [CometVerse](https://play.cometverse.com/), and they also wished to pay transaction fees for the users. Therefore, [Totient Labs](https://www.totientlabs.com/) brought up [VIP-191](https://github.com/vechain/VIPs/blob/master/vips/VIP-191.md) to extend the transaction fee delegation protocol.

## Content

VIP-191 was proposed to extend the fee delegation protocol and to further explore its application scenarios, so as to make up for situations not covered by `MPP`. It includes:

1. Extending the TX model by adding Delegator signature.
2. Identifying VIP-191 enabled Txs by setting Fee Delegated true.

<!-- more -->

## Transaction construction

1. Construct unsigned TX and set `Fee Delegated Flag` to true.
2. Construct TX signature with both `TxOrigin` signature & `Delegator` signature(order of these does not matter).
3. Construct complete TX with the completed signature, and broadcast it to the blockchain.

## Transaction fee deduction

When executing transactions which enabled `VIP-191`, blockchain will (and will only) deduct transaction fee from the `Delegator`. As for others, `MPP` should be applied, or transaction fee should be deducted from the `TxOrigin` directly.

## MPP vs VIP-191

To summarize the differences between MPP and VIP-191:

+ `MPP`: Delegation relationship stored in the prototype of the account(on-chain), and <mark>once deployed it becomes active everywhere else</mark>.
+ `VIP-191`: Delegation relationship stored outside blockchain(an independent Delegator service), and the action of delegation is <mark>explicitly declared in TX and there should be no specific requirement for the content of TX</mark>.

## Applications

### Blockchain Application Developers

Application developers can set up `Delegator Service` exclusively for their own applications, for the purpose of providing users with transaction fee delegation services. Thereafter, users can try out applications without paying fees.

### Wallet Developers

Wallet developers can achieve `Self delegate`, which means when a user has multiple accounts, the wallet can provide users with an alternative function during payment confirmation, which allows users to choose other accounts beside `TxOrigin` to pay for the transaction fee, and the account is known as `GasPayer`. In this way, users can store VTHO in only one account, while other accounts don’t have to possess VTHO.

### Exchange Developers

Take the advantage of VIP-191, exchange developers can simplify the process of accumulating assets(especially tokens) and reduce VTHO consumption. Exchange can make an independent fee payer account to pay for all the transaction fees by setting a specific `Delegator Gateway`, and there is no need to transfer transaction fee to sender’s address: let sender initiates a VIP-191 enabled Tx, attaches the TxOrigin signature, and send it to the gateway. The gateway will then attach the `Delegator Signature` and send it to blockchain.

## Conclusion

Together, `MPP` and `VIP-191` constructed VeChain's `transaction fee delegation protocol`. It offers more possibilities to developers and achieves a more friendly user experience to all users.
