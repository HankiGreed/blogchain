---
title: "Getting Started With Ethereum"
date: 2020-06-10T12:02:37+05:30
draft: false 
lastmod: 2020-06-10T12 17:55+05:30
tags: ["Ethereum","Blockchain", "Smart Contract"]
categories: ["Beginner", "Tutorial"]
---

> With Ethereum 2.0 underway, There is not a better time to jump into the interesting world of Blockchain. 
> As a **Hello World !** program, Let's create a smart contract and walk through its working.

--- 
## What is Ethereum ?
Ethereum was conceptually started by [Vitalik Buterin](https://twitter.com/VitalikButerin), When he was trying to curb the problems of existing cryptocurrencies. As he states [(In this post)](https://vitalik.ca/general/2017/09/14/prehistory.html), his goal was to make Ethereum a platform for building digital money and creating smart autonomous programs that could efficiently handle digital assets. Later, with contributions from [Gavin Wood](https://twitter.com/gavofyork/), a slight change in vision ensued and Ethereum, whilst being a blockchain with it's own cryptocurrency, to a general computing platform. Without getting into the intricacies of the ethereum platform, Let's hit the ground running and look at some code. 

---
## How and where do I write code ?
> I've seen most blogs don't cover this rightaway, I thought it's worth knowing your options.:grin:
The most used programming language to write contracts in the ethereum ecosystem is [Solidity](https://solidity.readthedocs.io). There are many ways you can start writing solidity code. 

- ### Online

    | Method | Comments |
    |--------|----------|
    |[Remix IDE](https://remix.ethereum.org)| Online, No Config, Easy Contract Interaction| 
    |[Ethereum Studio](https://studio.ethereum.org)| Online, Can also run contracts with frontend| 

- ### Offline 

    *Below methods need to install things, You can follow their respective instructions.*

    | Method    | Comments |
    |-----------|----------|
    |[Truffle](https://trufflesuite.org/truffle)    |CLI to compile, link & Test contracts, Also other tools ([Ganache](https://trufflesuite.org/ganache) *Local Blockchain, Yay !*) |
    |[OpenZeppelin](https://docs.openzeppelin.com/cli/)|Test compile deploy *Update* Contracts, Has a great [Smart Contract](https://docs.openzeppelin.com/contracts) library |
    |[Embark](https://framework.embarklabs.io/)|A Swiss Knife for Ethereum, Not a really a beginner level tool|
    

> :zap: I plan to write a post OpenZeppelin's contract library, Lookout for it :zap:

--- 
## Grr, Show Me Some Code Already !!! :tiger:

*Okay, Sorry, Here We Go !*

### Copying and Running Code 
Let's write (* \<Ctrl C \>+\<Ctrl V\> :wink: !*) some simple code and then see it's working.

```sol
// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity ^0.6.9;

contract EnterEthereum {
    string myName;

    constructor () public {
        myName = "A Contract Has No Name !";
    }
    
    function getName () public view returns(string memory) {
        return myName;
    }

    function setName (string memory newName) public {
        myName = newName;
    }
}
```
- Head to [Remix IDE](https://remix.ethereum.org). Delete all the available files by `right-clicking` on them. Create a new file by clicking on the **(+)** button and name it. Copy the code above into the file. 
- On the left hand menu, click on the Solidity Logo to and hit the `Compile <filename>` button. Provided you didn't make mistakes copying, the code should compile rightaway.
- Click on the Ethereum Logo on the menu and and `Deploy EnterEthereum`. This should provide you with a `deployed contracts` menu below it.
- Now you can enter a name and set it, or retrieve the name by calling the name.
- After playing with the contract to your satisfaction :grinning:, come back here to understand it, I'll be here :smile:!.



### Let's disect this code :woman_health_worker: !

```sol
pragma solidity <sym><major_no>.<minor_no>.<patch_no>
```

First line in your solidity code will be to declare a compiler version that your program is targetting. The caret `^` signifies that the contract is compatible with any compiler version above the given one.
Omitting `^` makes the contract targer that specific version.Other possible symbols are `>, >=, \<, \<=`. 

> As of writing this post, Solidity Compiler lives in the version 0.6.9. According to the site, being in 0 major version, Even **minor version** increments can cause **breaking changes !**

Everything in solidity is either a Contract or a Library. Contracts are highly analogous to classes in object oriented languages. Librarys help you keep your code [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) by unifying some code for a particular type. Our contract first declares a name variable. 
> Variables can also have visiblity identifiers i.e, `public`, `internal` (analogous to `protected` in some OOP languages), `private`
> All variables are private unless specified otherwise.

Below it we have three functions

- Constructor

    A function called immediately upon the deployment of contract. Useful to initialize contract variables.

- getName
    Dissecting this function we see,
    - `public` - An identifier to emphsise the fact that this fucntion can be called by anyone accessing the blockchain.
    - `view` - Declares that the function does not modify any of the contract's variables.
    - `returns(string memory)` - Returns a string variable, available in the contract's memory.
    - Function's body is self explanatory
- setName
    Dissecting this one,
    - Absence of `view` - Since the function's body changes a contract's variable, this function can't be used with `view` modifier.
    - Since it doesn't return anything, It  has no return part either.

> ### A worthwhile observation
> Though `name` variable is `private` (Default Scope), Due the function `setName` being public let's anybody modify it.
> Beware of such patterns in your code !

**Congrat's on writing your first Smart Contract. Treat yourslef to something (:ice_cream:/:beers:)**

**You Deserve It !**
