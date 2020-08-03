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

## Decentralized Finance. (DeFi)

[Decentralized Finance](https://www.securities.io/what-is-defi/) is an effort to bring the benefits of distributed ledger technologies to the real world of finance. Instead of the financial institution [knowing their customers](https://en.wikipedia.org/wiki/Know_your_customer) to ensure their trust is well placed, you are able to transparently view all of the [Smart Contracts](https://blogchain.wtf/post/getting-started-with-ethereum/), security practices, security audits to effectively *Know Your Bank*. Even if you are not well versed with the jargons and tech related to the contract, you can still check the security audit, and see how many of the high severity bugs  have been fixed. That should give you a pretty good idea of the practices of that community. The known practices of DeFi have been the overcollateralized loans where you have to first lend some currency into their pool, use them as collateral to take some other currency as a loan. This system welcomes a whole lot of trading opportunities as is and they have been put to use for quite some time now. So what does flash loan bring to the table ?

## Flash Loans, The New Kid on the DeFi Block. *(Well Relatively !)*

Flash Loans, pioneered by [AAVE](https://aave.com), is a method of borrowing any amount of any currency with zero upfront collateral, but only using a **smart contract**. So in essence, you are able to borrow *any* amount of *any* currency you want (How much ever the exchange has, of course), *anonymously* without staking *anything*. 

![Wait, What ?](../../images/WaitWhatMatrix.webp)

- ### Okay , If that is possible, then what's the catch ?
    - The loaned amount has to be returned in the same transaction. If you don't, then the smart contract `revert`s your transaction so now it's like you never even borrowed the amount.
    > Since, the `revert` in Ethereum resets the state back to the previous one, Your balances are all invalidated, So it's like the currency note you are carrying suddenly became invalid. :wink: Any and all transactions that you performed with the borrowed amount now stand invalidated. 
    - You still pay the gas for the transaction.
    - You pay marginal fees to the Flash Loan Provider, If the transaction goes through properly.

- ### If we return the money as soon as we borrow, What's the use ?

    > All use-cases are listed in the [AAVE's blog](https://medium.com/aave/sneak-peek-at-flash-loans-f2b28a394d62)
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

> If fourth transaction doesn't make any sense, Just imagine it as a genie driving the price of WBTC to a sky high.

So, Outright the attacker a 71 ETH profit, but that isn't flashy :zap: enough.

The two positions are what give the attacker a whopping 350k USD profit. So, at the risk of sounding complicated, let's dissect this. When the attacker opened a short position in bZx, bZx bought 51 WBTC on behalf of him on Uniswap and at the time, Uniswap had (2817.77 ETH & 77.09 WBTC) making the exchange rate 

$${2817.77\ ETH \over 77.09\ WBTC} = {36.58441558441559\ ETH/WBTC}$$

But the moment bZx tried to buy WBTC on behalf of the attacker giving its 5637.2 ETH, The new price becomes

$${2817.77 + 5637.2 \over 77.09} = {109.67661175249707\ ETH/WBTC}$$

This stellar rise in the price actually [defaults](https://www.investopedia.com/terms/d/default2.asp) the position in bZx, as the borrwed WBTC's total value is now way over that was collateralized. So this position was left by the attacker to be liquidated by bZx as he got what he wanted, The stellar rise in the WBTC-ETH price. Now he can just return the 112 WBTC that he borrowed from Compound in **Step 2** at a much much greater value, giving him a hefty profit.

>  **Note**  
> The [Paper](https://arxiv.org/abs/2003.03810) considers this attack under-performing and goes on to explain which of the trade parameters 
> could have been altered to realize a much greater profit :flushed:, Do give it read as they solve it as an optimization problem, Some part of 
> which went way over my head.:astonished:

- ### So, What went wrong and can we blame Flash Loans ?

> Its obvious that without a flashloan an attack this huge would not be as easy. So are **_Flash Loans_** to blame ?

### Actually **No**
Though the flash loan provided huge amount of currency, It was up to Uniswap & bZx to not let a transaction that would default itself to go through.The bZx smart contract missed a sanity check which would not let transactions that default themselves go through, In this particular case. Though there was an implementation of this check, It just was not called in the said case. 

> The bug was fixed quickly, and there was not an attack of the same type again. That shows bZx's competency.

## Attack #2 (Oracle Manipulation)

### What is Oracle ?
The [_Openzeppelin Blockchain Glossary_](https://consensys.net/knowledge-base/a-blockchain-glossary-for-beginners/) says

>  **Note**  
>Typically, an oracle is any entity or person that is relied on to report the outcome of an event. In a blockchain network an oracle (human or machine) helps communicate data to a smart contract which can then be used to verify an event or specific outcome.

In DeFi ecosystem, Oracles are Decentralized Exchanges whose current state of assets is used to determine the price of a given asset. The benefit of it is being completely independent and On-Chain. The downside is that they can be manipulated.

The core of this attack wishes to manipulate a DEX *([Kyber](https://kyber.network/) & [Uniswap](https://uniswap.org)  Specifically)* to decrease the price of sUSD/ETH, then use a trading platform ([bZx](https://bZx.network)) which uses Kyber DEX as Price Oracle to borrow sUSD at a distorted price and in the final step, sell the borrowed sUSD at an exchange that doesn't use Kyber as a Price Oracle.

The attack managed to pull off a **_675k_** USD profit.

> I am not going to give you the exact steps of this attack as that would simply drag the post.

>  **Note**  
>The paper I referenced again states this to be underperforming and gives the parameters for an attack that would give **_1.1M USD_** :flushed: :money_with_wings: profit.

## Bottomline

Some takeaways from these attacks,
- The very benefit of being open to everyone is also the bane of Blockchain system as anyone can join the network, Implement their innovative protocol and let it loose in the wind and now its everyone's responsibility to check their code for faults that were not in scope before but are looming threats now.
- As a new protocol, method or a currency walks into the already overflowing DeFi system, it not only increases the  variety and options but also the [Attack Surface](https://en.wikipedia.org/wiki/Attack_surface).


