# Blockchain

Permissioned vs permisionless

TPS (transactions per second)

Blockchain network (also called cluster) is p2p network of nodes (called validators in Solana) exchanging with a copy of a ledger (blockchain). Typically 10 - 50 validators totaling 1000-2000 nodes[^validators]

* distributed state transition requires consensus


Smart contracts are run on VM (Sealevel for Solana, EVM for Ethereum)

Client is a program communicating with a cluster via transactions (composed of several intructions in terms of solana). 

Transactions are signed by the client by means of Public Key cryptography.

dApp -decentralized app is a UI (typically writen in some Javascript flavor) client 

Node which appends new transaction to the ledger is an elected leader.

Token is a unit of payments for computing resources of the cluster (running smart contract or validating its output). You can invest some tokens (delegate a stake) into validator gain a reward. Validator takes some fee (commission) for their services (in bitcoin by including a transaction with 25 BTC out of nowhere[^buterin]. Growing validators can offer lower commission. It favors large validators to become even larger because lower fee insentives investors to delegate their stakes at a lower commission[^validators oligopoly].

If validator behaves maliciously, you los
e your delegated tokens and also validator loses its delegated power and cannot earn more or even as before.

You can use a wallet which is a client allowing you to operate on your token account (send and receive).
To make stakes usually separate stake accounts are used. Solana: Tokens on the stake account can only be invested in the one validator the whole amount at one time. To invest to several validators simultaneously you should use several stake accounts.

How do you get your initial tokens to start? Crypto exchanges (like Binance) allow you to buy some tokens in exchange for a usual currency like US dollars.

Account is typically identified by public key.

Validators advertised their public key.

Bitcoin core can be used to build dApps
* with bitcoin scripting
* with some metaprotocols requiring to use trusted nodes
Or you should invent your own blockchain

Buterin suggested EVM to 

Classical consenus requires all nodes to participates which prevents scalability.
So either some nodes (called validators) are selected to validate transactions or another way of consensus is suggested (probabilistic consensus of Bitcoin called Nakamoto consensus, probabilistic Avalanche consensus).
Or the topology of network is changed
- it is sharded by feature (DeFi blockchain, smart contracts etc) (Cosmos blockchain)
- centralization - when there is central network to validate (Polkadot)

In case of validators pool with classic consensus we need some voting mechanism to allow for more even participation (quadratic voting, https://wiki.polkadot.network/docs/learn-phragmen)



[^validators]: [https://forums.solana.com/t/validator-information-thread/577/51](https://forums.solana.com/t/validator-information-thread/577/51)

[^validators oligopoly]: See, for example, Solana's validators market shares and commissions [https://solanabeach.io/validators](https://solanabeach.io/validators)

[^buterin]: (https://ethereum.org/en/whitepaper/)