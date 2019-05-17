---
title: Watch The Blockchain
date: 2019-05-16 21:49:12
tags:
---

## Introduction

Blockchain is a `chain of block` and block proposers keep proposing new blocks to it. Every new block means new status, when developing the blockchain apps, we may need to "watch" the blockchain then we know that is the time to update the status. I will show you the trick of `watching` the blockchain by [`Connex`](https://connex.vecha.in/#/).

## Ticker

Ticker is an API from Connex. In the doc, it is described as following:

> Ticker is a concept that describes chain increment, when there is a new block added to the chain, tickers will be triggered. This API will create a ticker which has a function that creates a `Promise` that will resolve when a new block is truly added, please be advised that it never rejects.

Ticker will make it possible for apps to be notified once there is new state on the blockchain.

## Use Cases

*Disclaimer: Codes in the article is just for demonstrating.*

Apps interact with the blockchain by sending transaction.Sometimes the app needs to wait for the transaction receipt. We can do it by the following code:

``` javascript
const ticker = connex.thor.ticker() // create a ticker
const txID = '0x3f02ce574583c65c1ac32b561ddfd54784af7aea6fb13b286c5314577e7e2f62'

let receipt = null
while(receipt === null){
    // waiting for the ticker
    await ticker.next()
    receipt = await connex.thor.transaction(txID).getReceipt()
}
```

Furthermore there are more cases that app needs to sense the blockchain increment, we can build a universal `EventBus` to dispatch the ticker event, if you are building a `Single Page Application`, this will help you a lot. Create the EventBus by the following code:

``` javascript
const EventBus = new EventEmitter()
const ticker = connex.thor.ticker() // create a ticker

while(true){
    // waiting for the ticker
    await ticker.next()
    EventBus.emit('tick)
}
```

In other components, you just need to import EventBus and play with it. So the waiting for receipt can be done like this:

``` javascript
const txID = '0x3f02ce574583c65c1ac32b561ddfd54784af7aea6fb13b286c5314577e7e2f62'

let receipt = null
EventBus.on('ticker', ()=>{
    connex.thor.transaction(txID).getReceipt(r=>{
        receipt = r
    })
})
```
