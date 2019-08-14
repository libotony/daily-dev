---
title: Fee Delegation-MPP
date: 2019-08-13 17:50:15
tags: [dapp,mpp,fee delegation,vip191,transaction fee]
---

Before we start, let’s clarify some concepts:

`TxOrigin`: the sender of transactions, an externally owned account.
`GasPayer`: the account pays the transaction fee.
`Fee Delegation`: transaction fee delegation(paid by a third-party), usually refers to the situation where the transaction fee is not paid by `TxOrigin`.
`Multi Party Payment`: Multi-party Payment Protocol, a type of Fee Delegation.

## The Story

We had experience in developing on Ethereum, and we figured a core problem to be tackled. People had difficulties in accepting the fact that `applications/tokens need to be funded by ETH as transaction fee`. In other words, it was not an easy task to promote blockchain applications, and it was even harder to have the users try out the applications for the `first time`. Therefore, we published the `prototype-based transaction fee delegation protocol` to lower the cost of using the blockchain application for users.

<!-- more -->

## Prototype

In the beginning, it did not have a remarkable name, and it was named `prototype`, the prototype of the accounts. This was a straightforward concept, but it did reflect the important feature that was included in its first design. The `prototype` was a derivative of the ethereum account model in VeChain. We know there were two types of accounts: internal(contracts accounts) and external owned (accounts that are controlled by private keys), and they had following attributes: address, balance, code-hash, and storage root. Besides, we have added several concepts (included but not limited to those features, we are only discussing what’s related to fee delegation protocol here).

+ `Master`: the master to the account; an abstract idea similar to `Owners`. The master of an EOA is the account itself by default and it’s the account deployed it when comes to a contract.
+ `User`: user of the account, usually refers to the user of contracts.
+ `Sponsor`: accounts would like to pay transaction fee.
+ `CreditPlan`: credit plan set by the master of an account. In other words, it’s the maximum fee amount a contract covers for its user.
+ `UserCredit`: available credit for a particular user.
+ `RecoverRate`: when the UserCredit is lower than the CreditPlan, it will recover at a certain rate. When the RecoverRate equals to zero, the disposable credit would not be recovered.

With a basic knowledge of the concepts above, it would be pretty easy to understand the `MPP`.

## Multi-party Payment Protocol

Understanding how transaction fee deducted is critical to understanding MPP:

+ Check whether the `Clauses` of the transaction share the same recipient account(`CommonTo`)
+ Check whether `TxOrigin` is the user of `CommonTo`
+ Check if there is sufficient user credit balance 
+ Check whether `CommonTo` has enough `VTHO` (fees will be taken out from Sponsor first)

If the transaction satisfies all the requirements above, then it is a delegated transaction, which is represented as `GasPayer ≠ TxOrigin`. In this case, `GasPayer` could be the Sponsor of `CommonTo` or `CommonTo` itself.

## Applications and limitations

The `prototype-based transaction fee delegation protocol` can be used to lower the cost of introducing new users and enables them to use the application for free. Just imagine, developers can set up the contract `prototype` in a few steps, and then users will be able to access blockchain applications without acquiring any tokens to pay for the transaction fee. In addition, {% label info@once the Prototype is set, it can be applied everywhere%}.~~As far as I’m concerned~~, this is one of the ass-kicking protocols in blockchain applications:-), we should have more users get to know it.

Well, now it’s time to talk about its limitations:

1. Since the transaction fee is deducted on transactions instead of clauses, the prerequisite for the delegated transaction is to have `CommonTo`.
2. Developers need to have it deployed before using, and must include every user in the user list of the contract.

In a nutshell,

{% note warning %}
It enables applications to pay transaction fees for its users, but it is not flexible enough to cover all kinds of situations.
{% endnote %}
