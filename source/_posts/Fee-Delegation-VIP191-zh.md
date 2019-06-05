---
title: Fee Delegation - VIP191
date: 2019-06-05 11:33:17
tags: [dapp,mpp,fee delegation,vip191,多方支付,代付,交易费]
---

在上一篇 {% post_link Fee-Delegation-MPP-zh %} 中，介绍了`MPP - 基于账户原型的交易费用代付协议`也提到了它在某些情况下是无法满足开发者需求的。尤其是 [Totient Labs](https://www.totientlabs.com/) 在开发应用 [CometVerse](https://play.cometverse.com/) 的时候组合了多个合约又希望可以为用户付交易费，所以[Totient Labs](https://www.totientlabs.com/) 提出了 [VIP-191](https://github.com/vechain/VIPs/blob/master/vips/VIP-191.md) 来扩展费用代付协议。

## VIP-191

VIP-191 的提出是为了扩展`费用代付协议`，扩展它的应用场景来弥补原有 `MPP` 无法覆盖到的情况。 它主要包含以下方面：

1. 扩展交易模型，加入 `Delegator 签名` 
2. 在交易模型中加入 `Fee Delegated Flag` 明确交易仅在它设置为true的情况下才是 VIP-191 代付的交易

<!-- more -->

## 构建交易

TODO

## MPP vs VIP-191

简略的去概括它们两个之间的区别的话：

+ `MPP`：代付逻辑的维护在账户原型中（链上），<mark>一次配置处处生效</mark>
+ `VIP-191`：代付逻辑维护在链下（独立的Delegator服务），<mark>代付行为是显式声明在交易当中且对交易内容无特殊要求</mark>

## 应用场景

### 应用开发者

作为应用开发者，可以为应用的用户提供交易费用代付服务，使得用户无需手续费即可体验应用。

### 钱包开发者

TODO

### 交易所开发者

降低资金的归集（尤其是代币）的复杂程度以及交易费的消耗。交易所可以通过设置统一的 `Delegator 网关`，无需预先转入交易费到发送方地址，只需发送方发起转账并加入 `TxOrigin` 签名，并将构建好的交易发送到网关，由网关加入 `Delegator 签名` 构建成完整交易，发送到区块链即可实现由一个独立的账户支付所有交易手续费。

