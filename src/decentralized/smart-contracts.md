Starting with Ethereum[^bitcoin], almost all blockchains provide a Turing-complete computation environment to execute programs (so-called smart contracts[^szabo], but in Solana they are called just programs) on chain. The execution is usually handled by some VM (the most popular choice is WASM-compatible VMs[^vms]) which is builtin in the node. The smart-contracts are written either in domain languages like Solidity and Cairo or in general languages like Rust (to be compiled to wasm or other ISA)[^languages].

Because of the whole domain complexity and available sources of contracts, any overlooked bug can bring a disaster[^bugs]  

If create such smart-contracts which allow some (based for example on the entrance fee) accounts in the blockchain to vote on some proposals, we will get a collective decision making, or so-called DAO (Decentralized Autonomous Organization)[^dao]. DAOs are [real](https://deepdao.io/organizations)[^wyoming].

[^bitcoin]: Bitcoin also has Bitcoin Script but it doesn't provide loops as a control structure.

[^szabo]: The concept of smart contract was proposed [by Nick Szabo in 1994](https://archive.ph/3vESb)

[^vms]: besides WASM-compatible VMs (like Wasmer), there is a Sealevel - Solana EBPf compatible VM with parallel execution, zkVM, Risc0 to name a few

[^languages]: Check [https://defillama.com/languages](https://defillama.com/languages) to see the most popular languages. But also take into account the target chain and estimate computation costs (aka "gas fees") as an execution of a smart contract is not free.

[^bugs]: see [https://rekt.news/leaderboard/](https://rekt.news/leaderboard/)

[^dao]: Despite evident pros (decentralized, automatic process) and cons (e.g. bugs in the smart contracts, longer decision process), in case of DAO governance with fungible tokens there is a problem of "whales" - large token holders which can shift voting or in case of DAO governance with the whitelisted accounts the risk of Sybill attack. They can even use so called [flash loans](https://ethereum.org/en/defi/#flash-loans)(when tokens are borrowed and returned within one block so no collateral is required) to gain a voting power in momentum.

[^wyoming]: A US state of Wyoming even [recognizes](https://www.coindesk.com/policy/2021/04/22/state-lawmaker-explains-wyomings-newly-passed-dao-llc-law/) DAOs as LLC to provide them a full legal status. See also [Securities Law: Social & Community Tokens](https://medium.com/seedclub/securities-law-social-community-tokens-3bb0ffaae62)