---
title: "Flash Loans, Flashier Attacks !"
date: 2020-07-15T09:55:29+05:30
lastmod: 2020-07-15T09:55:29+05:30 
draft: false
keywords: ["defi", "aave", "bzx"]
description: ""
tags: ["defi", "security", "aave"]
categories: ["DeFi", "Security"]
author: "Kiran Hegde"
mathjax: true
mathjaxEnableAutoNumber: true

---

> Most of the content in this post is either from or inspired by [this](https://arxiv.org/abs/2003.03810) paper. If you would rather read it,
> go ahead and skip this post.

## Decentralized Finance. 

[Decentralized Finance](https://www.securities.io/what-is-defi/) is an effort to bring the benefits of distributed ledger technologies to the real world of finance. Instead of the financial institution [knowing their customers](https://en.wikipedia.org/wiki/Know_your_customer) to ensure their trust is well placed, you are able to transparently view all of the [Smart Contracts](https://blogchain.wtf/post/getting-started-with-ethereum/), security practices, security audits to effectively *Know Your Bank*. Even if you are not well versed with the jargons and tech related to the contract, you can still check the security audit, and see how many of the high severity bugs  have been fixed. That should give you a pretty good idea of the practices of that community. The known practices of DeFi have been the overcollateralized loans where you have to first lend some currency into their pool, use them as collateral to take some other currency as a loan. This system welcomes a whole lot of trading opportunities as is and they have been put to use for quite some time now. So what does flash loan bring to the table ?

## Flash Loans, The New Kid on the DeFi Block. *(Well Relatively !)*

Flash Loans, pioneered by [AAVE](https://aave.com), is a method of borrowing any amount of any currency with zero upfront collateral, but only using a **smart contract**. So in essence, you are able to borrow *any* amount of *any* currency you want (How much ever the exchange has, of course), *anonymously* without staking *anything*. 

![Wait, What ?](../../images/WaitWhatMatrix.webp)

- ### Okay , If that is possible, then what's the catch ?
    - The loaned amount has to be returned in the same transaction. If you don't, then the smart contract `revert`s your transaction so now it's like you never even borrwed the amount.
    > Since, the `revert` in Ethereum resets the state back to the previous one, Your balances are all invalidated, So it's like the currency note you are carrying suddenly became invalid. :wink: Any and all transactions that you performed with the borrowed amount now stand invalidated. 
    - You still pay the gas for the transaction.
    - You pay marginal fees to the Flash Loan Provider, If the transaction goes through properly.

- ### If we return the money as soon as we borrow, What's the use ?

    > All use-cases are listed in the [AAVE blog](https://medium.com/aave/sneak-peek-at-flash-loans-f2b28a394d62)
    - [Collateral Swapping]( https://twitter.com/JordanLzG/status/1230484691679088640 )
    - Interest Rate Change
    - Self Liquidation
    - Arbitrage Opportunities
    > Mind you, all the transactions are to be executed by a Contract.

> I'll now stop about Flash Loans as it's a intricate topic to explain.
> Go through [this](https://aave.com/flash-loans) for more details. Let's now get into the attacks that happened.

## Attack #1 (Pump and Dump)

> The following explanations are superficial, If you need exact details, read the paper or disclosures by bZx.

This attack involved pumping an exchange with enormous amount of one currency, borrowing the other, Which will lead to an imbalance of the prices

|Steps| Assets|
|------|--|
|Borrow 10,000 Ether from [dydx](https://dydx.exchange) (At the time of writing this, It values to 2.3M USD :money_with_wings: )| 10k ETH|
|Deposit 5,500 ETH to [Compond](https://compound.finance)|4.5k ETH, Position in Compound|
|Using the deposited ETH as collateral loan 112 [WBTC](https://wbtc.network/) (Wrapped Bitcoin)|4.5k ETH, 112 WBTC|
|Take up a short position with 5x Margin at [bZx](https://bzx.network) with 1300 ETH against WBTC.|3.2K ETH, 112WBTC, Position in bZx|
|Exchange the 112 WBTC to 6871 ETH in [Uniswap](https://uniswap.org)|10,071 ETH, Compund & bZx Positions|
|Payback the 10k ETH flash loan to dydx| 71 ETH, Compond & bZx positions|

> If fourth transaction doesnt make any sense, Just imagine it as a genie driving the price of WBTC to a sky high.

So, Outright the attacker a 71 ETH profit, but that isn't flashy :zap: enough.

The two positions are what give the attacker a whopping 350k USD profit. So, at the risk of sounding complicated, let's disect this. When the attacker opened a short position in bZx, bZx bought 51 WBTC on behalf of him on Uniswap and at the time, Uniswap had (2817.77 ETH & 77.09 WBTC) making the exchange rate 

$${2817.77\ ETH \over 77.09\ WBTC} = {36.58441558441559\ ETH/WBTC}$$

But the moment bZx tried to buy WBTC on behalf of the attacker giving its 5637.2 ETH, The new price becomes

$${2817.77 + 5637.2 \over 77.09} = {109.67661175249707\ ETH/WBTC}$$

This stellar rise in the price actually [defaults](https://www.investopedia.com/terms/d/default2.asp) the position in bZx, as the borrwed WBTC's total value is now way over that was collateralized. So this position was left by the attacker to be liquidated by bZx as he got what he wanted, The stellar rise in the WBTC-ETH price. Now he can just return the 112WBTC that he borrowed from Compound in **Step 2** at a much much greatervalue, giving him a hefty profit.

> The [Paper](https://arxiv.org/abs/2003.03810) considers this attack underperforming and goes on to explain which of the trade parameters 
> could have been altered to realize a much greater profit :flushed:, Do give it read as they solve it as an optimization problem, Some part of 
> which went way over my head.:astonished:

