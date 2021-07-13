# Analysis of on-chain privacy

When Bitcoin launched in 2009, many in the Cypherpunks community had concerns about the privacy of the users on the protocol. One of the shortcomings of Bitcoin (in comparison to Gold and Cash) is that it lacks fungibility. Fungibility is a simple property of money such that there are no differences between two amounts of the same value ie they can’t be differentiated.

Here’s Satoshi talking about the issue and Zero-Knowledge proofs

![Satoshi screenshot](https://user-images.githubusercontent.com/25363622/125444238-ef924150-c5c3-411d-9c46-562303b5830b.png)

Source: [bitcointalk](https://archive.li/ow44s) (Note: If you can't see the above image, please try switching to an incognito tab or disabling any adblockers)

## Privacy on Bitcoin
Bitcoin works with an unprecedented level of transparency that most people are not used to dealing with. All Bitcoin transactions are public, traceable, and permanently stored in the Bitcoin network. These transactions can be searched through [blockchain explorer](https://www.blockchain.com/explorer) and [etherscan](https://etherscan.io/) (for Ethereum) by anyone. The bitcoin you might have today might even be tied to a history of transactions involving illegal activities that you might not want to be implicating yourself with.

There are companies like [chainalysis](https://www.chainalysis.com/chainalysis-reactor/) which offer services like doing analysis about linkability of wallets. And market, traceability as a **feature instead of a bug**, which most businesses and individuals will disagree with.

![Chainalysis screenshot](https://user-images.githubusercontent.com/25363622/125444279-201ccd5b-0876-4e5b-9554-5ab9cde50d2d.png)

Outside of pseudo-anonymity that addresses aren’t linked to an identity of a person, there isn’t much to anonymity on bitcoin’s base layer.

Some methods to improve privacy while using Bitcoin. Each of them has its own tradeoffs -

**Centralized Mixing** - Using a third party like an exchange or a mixing service. You will send them some coins, and they will send back the same account, from another stash of coins. For anyone looking from the outside, the two transactions won’t appear to be connected at all. However, you must trust that the third-party service won’t keep logs. They can also steal your coins by just avoiding sending them back. Lastly, the third-party service must have a lot of liquidity (many bitcoins sitting idle waiting for mixing).

**Tumblebit** - A trustless version of centralized mixing. It is still run by a single server, but you don’t have to trust that server with your privacy or with the safety of your funds. The Legality of using such services might vary and be subjected to different rules in each jurisdiction.

**Lightning network** - It moves many transactions off-chain, most network participants are not aware of them, so better privacy can be achieved. Some actions may leak information (for example, opening and closing channels on-chain).

**CoinJoin** -

1. N parties come together over some anonymous channel, eg. Tor. They each provide a destination address D[1] ... D[N].

2. One of the parties creates a transaction that sends one coin to each destination address.

3. The N parties log out and then separately log in to the channel, and each contributes one coin to the account that the funds will be paid out from.

4. If N coins are paid into the account, they are distributed to the destination addresses, otherwise, they are refunded.

![Conjoin visual explanation](https://user-images.githubusercontent.com/25363622/125444344-16d1c56e-4440-45e1-8cb5-2b12fdc62f2d.png)

[Source](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/)

**Taproot** and **Schnorr signatures** are said to help in the privacy problem as well. Some efforts are ongoing with the payment messages API to avoid tainting multiple addresses together during a payment. Various work and research are also being done to develop other potential extended privacy features like being able to join random users transactions together.

Numerous other projects have tried to solve the privacy problem. We will talk about some of the popular projects & concepts below.

## What is a zero-knowledge proof (ZKPs)?

Zero-knowledge proofs are a scientific breakthrough in the field of cryptography, they allow you to prove that a statement is true without revealing the statement itself. For example: if you want to prove you are old enough to drink alcohol, you generally have to show your ID card which reveals a lot of information about you beyond just your age. Your ID card can reveal your address, your face, your height - all of that has nothing to do with your age. A zero-knowledge proof could let you prove that you are old enough and only that. Cryptocurrencies use zero-knowledge proof called **zk-SNARKs**.

## What is a Ring Signature?

In cryptography, a ring signature is a type of digital signature that can be performed by any member of a group of users that each have keys. Therefore, a message signed with a ring signature is endorsed by someone in a particular group of people. One of the security properties of a ring signature is that it should be computationally infeasible to determine which of the group members' keys was used to produce the signature.

For example, a ring signature could be used to provide an anonymous signature from "a high-ranking White House official", without revealing which official signed the message.

## What is a Stealth Address?

Stealth addresses allow and require the sender to create random one-time addresses for every transaction on behalf of the recipient. The recipient can publish just one address, yet have all of his/her incoming payments go to unique addresses on the blockchain.

## Monero

### General

Monero was launched in 2014. Its development is **community funded**. Monero uses three different privacy technologies: **ring signatures, ring confidential transactions/Bulletproofs, and stealth addresses**. These hide the **sender, amount, and receiver** in the transaction, respectively. **All transactions** on the network are private. Though [Advanced forensics and analytics companies claim to be able to trace these types of transactions](https://news.bitcoin.com/ciphertace-allegedly-builds-monero-tracing-tools-xmr-proponents-disagree/), the community doesn’t believe that.

A ring signature makes use of your account keys and a number of public keys (also known as outputs) pulled from the blockchain. Over the course of time, past outputs could be used multiple times to form possible signer participants.

It is generally agreed that Monero **leaks more information** than Zcash and other solutions which use ZKPs for all the information and not only the amount.

Monero used to have 2 network upgrades (hard forks) a year, but this is not the case anymore. The frequency has been decreased now.

### Protocol Design

As long as the encrypted output amounts you create are equal to the sum of the inputs that are being spent (which include an output for the recipient and a change output back to yourself and the unencrypted transaction fee), then you have a legitimate transaction. The amounts are encrypted using **Pedersen commitments** what is achieved is that no observers can tell the amounts of the inputs and outputs, but they can do the math on the Pedersen commitments to determine that no Monero was created out of thin air.

Monero does not have a hard block size limit. Instead, the block size can increase or decrease over time based on demand. It is capped at a certain growth rate to prevent outrageous growth

As of 2021, the full blockchain is around **95-100GB**. A pruned blockchain is about **30GB**.

Monero scores very **well on confidentiality** but **poorly on auditibility**. Monero offers a ViewKey feature to let a third party review a user’s transactions. However, it only allows seeing incoming transactions, not outgoing ones.

### Dev Experience

Monero seems to have the best support among privacy coins for Devs making wallets, apps around Monero. Also, they have the support in most programming languages, this makes it easier for people to implement their own wallets or audit existing wallets before using them, which can enable more trust of the community in the protocol.

This example creates a client connected to monero-wallet-rpc then creates a wallet.

![Snippet 1](https://user-images.githubusercontent.com/25363622/125444381-beb805ed-3f95-4800-a668-01bba990cced.png)

Wallet transactions, transfers, and outputs can be queried by their properties using query objects.

![Snippet 2](https://user-images.githubusercontent.com/25363622/125444416-7519a714-cd7f-4fea-a4a4-6f528ee0c706.png)

The following is an example of sending funds using monero-javascript

![Snippet 3](https://user-images.githubusercontent.com/25363622/125444481-2f502383-0b0a-487b-948b-d5d9cab4f11b.png)

## Zcash

Zcash was launched in 2016. It is based on the peer-reviewed Zerocash protocol, which was published in the IEEE Security & Privacy conference in 2014. Its key differentiators are fast block times (**75 seconds**), low fees (default **0.0001 ZEC**), and large block sizes (**2 MB**). Its protocol development is self-funded with community governance. It uses block rewards to fund the development.

Zcash is a code fork of the bitcoin protocol. It enables privacy-preserving transaction data using **zk-SNARKs**. Its PoW algorithm is different than bitcoin, but **not ASIC resistant**. Zcash is closely monitoring the effectiveness of PoS, to maybe upgrade to it in the future.

With a fully-shielded Zcash transaction, the **sender, recipient, and amount** are fully encrypted and completely private. The owner can grant view keys for shielded transactions. Transparent Zcash transactions are similar to BTC in that the sender, receiver, and amount are visible on-chain. Zcash’s use of Groth 16 proofs hides both the **transaction amount** and the **transaction graph**. Zcash is trusted by regulated exchanges such as Gemini and Coinbase.

The adoption of shielded transactions is low. Some people consider it a con that privacy isn’t enabled by default and there are many cases of unwanted transparent transactions by mistake. There are some competitors which do all the things Zcash but with default privacy. Transparent addresses support multi-signature transactions but shielded addresses **do not**. zkSNARKs are new and few people understand them deeply.

![Zcash transactions metrics](https://user-images.githubusercontent.com/25363622/125444504-96ae080f-27a0-48dc-8c09-470b0684e68c.png)

Few points, one can use to judge the development of Zcash -

Zcash Client supports many Linux Systems and macOS, but **not windows**.

Zcash network upgrades(hard forks) two times a year (approx).

**Electric Coin Company** and the **Zcash Foundation** both contribute to research, the protocol, reference client, and public communications. The Zcash Community Advisory Panel (ZCAP) votes on major community decisions. They have been funded by founder rewards, private capital(with notable investors), and community development funds.

For the first 4 years, 10% of the Zcash monetary base went to a fund called the Founders' Reward.

## Iron Fish

Iron Fish is live on testnet only. By using **ZKPs**, private transactions are made that fully hide the **sender, recipient, and amount like Zcash**. **Every transaction** is private, the owner can grant view keys for the transactions. It uses a PoW algorithm.

It has been funded using **private capital from notable investors**. Iron Fish promises to become a shielded layer for many assets, even cross-chain assets. They promise to be focused on accessibility alongside privacy. They say that a user will be able to run a full node directly in the browser in future iterations.

The Iron Fish networking layer supports WebRTC with WebSockets, through this a user can hide their IP address.

## Privacy on Ethereum

Ethereum’s transactions are also transparent like bitcoin’s. One can see the history of balance on an address through etherscan and all the transaction related to it. Most of the techniques used for Bitcoin transactions to enhance privacy can be used for Ethereum as well like **mixers, State Channels, Plasma chains**. Some other solutions have also been tried on Ethtereum like specialized smart contracts to obfuscate funds.

A scalability technique called **Rollups**, makes it possible for a single on-chain transaction to handle a series of secure off-chain transactions. The on-chain transaction “rolls up” the off-chain transactions, so to speak, using the on-chain transactions more efficiently. **Zk-rollups** that use ZKPs to enhance scalability and privacy on Ethereum L2. There are many ZK-solutions that try to solve the privacy problem on L2.

![Privacy visual](https://user-images.githubusercontent.com/25363622/125444557-d35790c2-686f-4d44-a753-a0ec904a359d.png)

[Source](https://www.youtube.com/watch?v=XpRzKqEfpP4)

Some critics argue that adding privacy over layer 1 is fundamentally flawed as L1 is always leaking information. And privacy should be added on base layer transactions.

## Tornado Cash

Tornado Cash is a decentralized protocol based on **ZKPs** which uses Ethereum’s ZK-rollups and runs on the Ethereum layer 2. It uses smart contracts to interact with the base layer. Only the user is able to link their deposit and withdrawal.

The Tornado Cash anonymity mining protocol is used to mine TORN, the governance token of Tornado Cash.

**Relayers** are used to withdraw to an account with no ETH balance. The relayer sends a withdrawal transaction and takes a part of the deposit as compensation (the protocol itself does not collect any fees). The relayer cannot change any withdrawal data including the recipient address.

They offer a compliance tool as well to generate a report which proves your source of funds.

![Tornado Cash metrics](https://user-images.githubusercontent.com/25363622/125444585-8b75bcac-361c-403c-bf90-1e7fc72e8959.png)

The adoption of TornadoCash is impressive.

## Dash

Dash was launched in 2014. It has a DAO-like structure, in which some advanced functions can be done by “**masternodes**“. Running a masternode requires ownership of **1000 Dash**. One kind of transaction which a masternode facilitates is ‘PrivateSend‘, which provides some privacy using **mixins**. Participating user’s unspent Dash are mixed before executing a transaction. Dash is considered the **most centralized** of all major privacy coins. While Dash does have advantages in terms of its **scalability** because of the centralization.

## What is Mimblewimble?

Mimblewimble is a crypto protocol. The idea has been around for decades, although the name and its application to cryptocurrencies are recent. Mimblewimble uses a form of elliptical-curve cryptography that requires smaller keys. In a network that is using the Mimblewimble protocol, there are no addresses on the blockchain. The nature of the protocol allows for private transactions that are optionally **auditable using view keys**.

Transactions are **lightweight**, they consist of just a number for the input, another number for the output.

**Cut-through** - When blocks are created, if a transaction is spending outputs from another transaction in that same block, the outputs from the spent transaction and the inputs from the spending transactions can be removed from the block! When the next block is mined, the two blocks can be cut-through in the same way, until the entire chain has been cut through. This not only saves space but also means that we end up with a chain that is composed only of unspent outputs, so the chain information can’t be used to link transactions. The Validity of the history that brought us to this state can be confirmed by storing only the headers of the previous blocks, which holds the proof of the validity of previous states.

The theoretical space savings, as well as the privacy, offered through the use of the Mimblewimble protocol is evident. By pruning the blockchain of unnecessary transaction data, scalability becomes less of an issue because more transactions can be included in a single block. Due to the **obfuscation of inputs and outputs**, as well as the **elimination of public addresses** in Mimblewimble transactions, true anonymity, as opposed to pseudonymity, is achieved.

One **drawback** of Mimblewimble is that, while a cut-through blockchain doesn’t retain enough information to link transactions, this information **does** exist when the transaction is first created (before cut-through).

Currently, Mimblewimble can only be integrated into the **Bitcoin network as a sidechain**. On this sidechain, users would be able to move bitcoins onto it and transact with an increased level of privacy.

Two popular implementations of this protocol are Grin and Beam. These implementations are new and lack important things like quality hardware wallets.

## Grin

Grin was launched in 2019. It uses a **community funding** model. There’s no ICO or token pre-mining, and the developers are all volunteers. Grin is **less accessible** for users outside the dev world like it has less support for GUI wallets.

Grin’s goal is to be used as a **traditional currency** with many transactions, instead of a store of value. It uses two different Pow algorithms for GPUs and CPUs.

It embodies many of the properties of the Mimblewimble protocol -

* No addresses

* No visible transaction amounts

* No transaction history

* No fixed supply

## Beam

Beam was launched in 2019. The parent company has collected **VC funding** and hired a development team. Due to this, it has faster development than Grin. Its release did not include an ICO or pre-mine. Beam’s foundation relies on the **20% of the block rewards** it receives to support the development of its network for the first 5 years. Beam is more accessible than Grin for users outside dev world.

Beam’s goal is to be a private **store of value**, with the addition of **second-layer solutions** like Lightning to allow for day-to-day usage. It uses a Pow algorithm. With each block produced containing **1000 transactions**, with a generation time of approximately **1 minute**. Beam supports around **17 transactions/s**. To promote decentralized mining on the Beam network, the protocol plans to stay ASIC resistant by performing hard forks when necessary.

## Final Thoughts

That’s it, folks. The cryptocurrency world is a high-stakes battle of ideas. In the privacy coin world, different projects are making different tradeoffs. I hope you now have a better understanding of these projects and the ecosystem. Let me know if I got some information wrong. This for originally [published on substack](https://ishankbahl.substack.com/p/analysis-of-on-chain-privacy)

## License

Shield: [![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]

This work is licensed under a
[Creative Commons Attribution-ShareAlike 4.0 International License][cc-by-sa].

[![CC BY-SA 4.0][cc-by-sa-image]][cc-by-sa]

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-image]: https://licensebuttons.net/l/by-sa/4.0/88x31.png
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg
