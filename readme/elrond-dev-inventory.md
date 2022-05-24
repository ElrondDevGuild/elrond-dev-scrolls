---
description: Introduction to tools, potions, spells... all what real Elrond Dev needs
---

# Elrond Dev Inventory

Let's put together all the tools required to become the real Elrond Dev warrior. The purpose of this is to try to make the lives of new developers simpler and show them the way.

Depending on your Elrond Dev warrior 'class', you will need different tools. There are two areas: writing smart contracts and interaction with smart contracts (frontends, CLI tools, apps, etc.).

### Smart contract mage

For writing smart contracts on the Elrond network, you would need to grasp the secrets of [Rust](https://www.rust-lang.org/) programming language, which has become very popular in the blockchain ecosystem. Don't worry. It shouldn't be so hard with the Elrond tooling, even for someone who never programmed in Rust.

The Elrond Virtual Machine supports any language which can compile the code to [WASM](https://webassembly.org/). In practice, it will depend on the actual available frameworks and their completeness. When writing this, the only complete framework is Rust-based [elrond-wasm-rs](https://github.com/ElrondNetwork/elrond-wasm-rs).

Btw. You can read more about the Elrond WASM Virtual Machine [here](https://docs.elrond.com/technology/the-elrond-wasm-vm/).

Rust framework will be your main sword!

There are also other tools. One of them will be [VSCode IDE](https://marketplace.visualstudio.com/items?itemName=Elrond.vscode-elrond-ide), an extension for VSCode which will make your life easier. It will allow you to build the smart contract, test it and run previously prepared snippets for interactions.

If you don't want to use the VSCode extension, your tool for the same operations will be [erdpy](https://docs.elrond.com/sdk-and-tools/erdpy/erdpy/), which is a Python-based CLI and SDK. We will learn more about it later, so keep reading. Btw. the VSCode extension also uses it under the hood.

Also, remember that we don't want to copy the official documentation, so that you will find a lot of information about smart contracts in the official docs. Start from here: [Developer reference](https://docs.elrond.com/developers/developer-reference/elrond-serialization-format/).

Soon, you will also find a few other 'scrolls' that will focus more on the details here. We already have a very simple example of a smart contract. Check it here: [elrond-simple-sc](https://github.com/ElrondDevGuild/elrond-simple-sc). It will be a base for documenting all the details of how it is built, how to interact with it, test it, deploy, upgrade, and more.

Oh, I almost forgot. It is always good to train with masters. Here you will find examples of smart contracts from real-world written by the Elrond core team masters:&#x20;

* [elrond-wasm sc examples](https://github.com/ElrondNetwork/elrond-wasm-rs/tree/master/contracts/examples)&#x20;
* [ElrondNetwork Smart Contracts](https://github.com/orgs/ElrondNetwork/repositories?q=sc\&type=all)

### Dapps summoner

The second area for the development is related to the Dapps and interaction with smart contracts and the Elrond network itself.

Here you will also need a couple of tools in your inventory.

This 'class' of the Elrond developer can have some subclasses. You can be a master in Typescript or Python. Both will be good for CLI tools, scripts, etc. Additionally, Typescript or JavaScript will be helpful when writing frontends.

Previously mentioned [erdpy](https://docs.elrond.com/sdk-and-tools/erdpy/erdpy/) is a powerful gem in your inventory. It can do a lot. It can be used as a dependency in Python projects. It can also be used as a standalone CLI tool. Here you will find all operations you can do with it: [erdpy CLI docs](https://github.com/ElrondNetwork/elrond-sdk-erdpy/blob/main/erdpy/CLI.md). And here, you will discover how to install it: [Installing erdpy](https://docs.elrond.com/sdk-and-tools/erdpy/installing-erdpy/).

Typescript SDK is called [erdjs](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs/), and believe me, mastering erdjs is a powerful path. There are only a few masters. Become one!

The Erdjs SDK can be used in the [NodeJS](https://nodejs.org/en/) environment and the browser. It has all the tools required for interaction with the network and smart contracts. It is split into a couple of more libraries to keep it as small as possible. These are listed here: [erdjs packages list](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs/).

There is also REST API. You will find Swagger docs here: [api.elrond.com](https://api.elrond.com/).

We will focus on more details and what each library can do in the following articles.

You can learn more about what is possible with erdjs here: [erdjs cookbook](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs-cookbook/).

Finally, ready-to-use frontend tools like templates for the React/Nextjs ecosystem are also available. Each of them uses the erdjs SDK as a fundamental tool. The official one is [dapp-core](https://github.com/ElrondNetwork/dapp-core), designed to be used with standard React apps. It is also probably the most developed one, with many components, hooks, and helpers. For the NextJS framework, there is a community base template included here in the Guild: [nextjs-dapp-template](https://github.com/ElrondDevGuild/nextjs-dapp-template), where you will also find links to other community-based solutions.

### Ok, so what other tools and services will be helpful?

What wasn't mentioned yet? Let's see.

Of course, wallets, this is crucial for you to start working. Imagine a warrior without money, huh?

Here is the list of all official wallets:&#x20;

* [wallet.elrond.com](https://wallet.elrond.com)&#x20;
* [testnet-wallet.elrond.com](https://testnet-wallet.elrond.com)&#x20;
* [devnet-wallet.elrond.com ](https://devnet-wallet.elrond.com)
* [https://maiar.com/](https://maiar.com/)
* [Maiar DeFi Wallet - browser extension](https://chrome.google.com/webstore/detail/maiar-defi-wallet/dngmlblcodfobpdpecaadgfbcggfjfnm)&#x20;
* Ledger Nano is also supported.

Do you want a quick try with smart contracts and interactions without any configuration? A Web IDE allows you to write, build, and even deploy the smart contract, and you can also load a couple of examples. Check it out: [Elrond Smart Contracts Playground](https://play.elrond.com/).

Working with the blockchain and smart contract will also require using some explorers. Here are the official ones:&#x20;

* [explorer.elrond.com](https://explorer.elrond.com)&#x20;
* [testnet-explorer.elrond.com](https://testnet-explorer.elrond.com)&#x20;
* [devnet-explorer.elrond.com](https://devnet-explorer.elrond.com)

You would probably also need the faucet. There are official faucets in the testnet and the devnet web wallets linked above, but there is also a community-based one here: [r3d4 faucet](https://r3d4.fr/faucet).

What is worth linking here is the [Elrond Converters web app](http://207.244.241.38/elrond-converters/). It is probably used very often in daily work.

### Where else a young Padawan could search for help?

Besides our welcoming Guild, there are also more suitable places for that. You could check:&#x20;

* [The Telegram Elrond dev group](https://t.me/ElrondDevelopers)&#x20;
* [StackOverflow questions tagged 'Elrond'](https://stackoverflow.com/questions/tagged/elrond)

Let us know if you would like to write something or have any ideas about what could land here in our scrolls library. Check more info about it here: [What kind of topics are most unclear, and what is the biggest obstacle to starting developing on Elrond?](https://github.com/orgs/ElrondDevGuild/discussions/2).
