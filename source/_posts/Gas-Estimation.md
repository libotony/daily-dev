---
title: Gas Estimation
date: 2019-05-20 13:24:06
tags: [dapp,wallet,sdk]
---

Gas is an unit that measures the transaction cost in EVM ecosystem. It is well known that transfers native coin(e.g. VET, ETH) needs 21000 gas to cover the transaction fee, but regarding the contract creation and execution, the gas cost is not a fixed value. The gas cost depends on the `state` on which executed the tx, this is related with EVM's gas consumption mechanism. One transaction packed in different block could result in different gas cost.

Transaction gas is composed with two parts, `Intrinsic Gas` and `Runtime Gas`. The `Intrinsic Gas` for a transaction is the amount of the transaction used before runtime executes the transaction. 

 
**intrinsicGas = txGas + SUM(clause.typeGas + clause.dataGas)**

`Intrinsic Gas` is deterministic for an transaction while `Runtime Gas` is dynamic.We have a way to evaluate a transaction's `Runtime Gas` by the API `POST /accounts/*`. As stated above, the evaluated gas just based on the current `BestBlock's State`. The gas cost may change when you send the same transaction to the blockchain with the evaluated gas. So the trick is:

**You need to increase the evaluated gas before sending to the blockchain.**

This means differently for different individuals:

+ For `DApp Developer`: You'd better specify gas for every transaction since you knows everything of your, otherwise you are relying on wallet or SDK to evaluate gas for you.
+ For `Wallet or SDK Developer`: if you dev specifies gas, you need to follow it. If not you need evaluate gas for your dev.

How much do we increase becomes important. As a contributor to thorify, I increased the evaluated gas by 20% to make sure the transaction won't run out of gas. And `sync` did the same thing.

> Life could be just that easy, I hope.

This mechanism works fine for a very very long time. Until my colleague tell me that he failed to buy the summit discount ticket using sync, the transaction run out gas. I was confused, it's a simple contract, simple as `Hello World`:

``` javascript
function buyXNodeTicket(uint quantity) public payable returns(bool) {
    require(msg.value / discountPrice == quantity, "Value sent must be a form of the discounted price");
    require(thorNodeContract.isX(msg.sender), "Message sender must be a X node");
    
    recipient.transfer(msg.value);
    return true;
}
```

It's just checking the ticket amount and the transferred VET amount and send the value to the recipient immediately, just as easy as you can thought.Furthermore, I found a transaction that buying same amount of ticket, the gas used(29277) in the succeed transaction is lower than the failed transaction(30840)(Transaction gas is greater), that makes me feel confused very much, why does it run out of gas with more than needed gas.

After some minutes digging with the team, we found a possible clue. The gas cost of op_cod `CALL` with value transfer has at least a fixed gas cost of 9000, after the `CALL` operation finished the vm will return the left over gas. Total gas used for `CALL` is much lower than 9000 since there is no code to run for the recipient. I traced the transaction and got the `CALL` only cost no more than 1000 gas. But to make the transaction succeed, you need at least 8000 extra gas for it to make this step's gas deduction succeed. So the trick failed since the evaluated gas is a small amount :(. But we upgraded it by:

**Increase the evaluated gas by a fixed amount: 15000.**

> EVM make the `CALL` with value transfer a fixed gas cost other than the total left over gas for reason, mainly for preventing attacks.
