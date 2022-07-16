---
title: Issue ESDT - example of setup and interaction using the erdjs SDK
description: Setup the erdjs workflow and issue an ESDT token in NodeJS enviroment.
---

Before we dig in, please be aware that this article is for developers who code in JavaScript/Typescript and know the basics of working with a NodeJS environment.

The Elrond Standard Digital Token is a built-in standard for custom tokens on the Elrond blockchain. It means anyone can create their token with similar properties to native EGLD.

If you don't know much about ESDT yet, please read about it here: [Introduction to ESDT tokens](https://docs.elrond.com/developers/esdt-tokens/#introduction). You will find a more general description of all transactions. It is essential to know that, but here we will focus more on the [erdjs SDK](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs/) and how to use it to issue ESDT tokens. So probably a more practical approach.

Before that, it is worth mentioning that operations related to the ESDTs don't require custom smart contracts. Nevertheless, smart contracts can also manage ESDTs in the same way as standard accounts.

This article will be the first in a series about erdjs and tokens-related operations. We are starting by issuing the ESDT token.

To start learning and playing around, it is crucial to understand what [erdjs](https://github.com/ElrondNetwork/elrond-sdk-erdjs) is.

It is a set of functions and helpers that simplifies interaction with the Elrond blockchain and custom smart contracts. The tool is written in Typescript and can be used in NodeJS or browsers.

It is the main package, but there are a couple of complementary libraries. You will find them here: [erdjs SDK libraries](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs/).

## Initial setup

Let's use the devnet for learning purposes, but in the end, you will understand how to reuse the same knowledge and switch to the mainnet.

Each transaction made with erdjs (and issuing the ESDT token is such a transaction) requires two things:

1. Wallet to be able to sign transactions
2. Network provider - tools and configuration for the API endpoint

These two we will split into more granular areas. Let's take the wallet first. We already have some information about how to work with the PEM files here [Use PEM file wallet](https://elrond-dev-guild.gitbook.io/scrolls/erdjs/how-tos/use-pem-file-wallet). You can check this out first.

The PEM file is a file with your private key, so don't share it with anyone. It isn't the most secure way to interact, because of human factor, but it is probably the simplest and fastest. You can also use the wallet JSON file with a password, and You would probably use the Ledger hardware wallet or Maiar Mobile app in a browser environment. But for simplicity and because we will work in the Nodejs environment, only locally, let's use a PEM file.

What you would need is to create such a file. You can do this in two ways:

- using the [erdpy](https://docs.elrond.com/sdk-and-tools/erdpy/erdpy/) tool, check how here: [Deriving the PEM file](https://docs.elrond.com/sdk-and-tools/erdpy/deriving-the-wallet-pem-file/)
- or you can also use erdjs based tool: [Buildo Begins](https://github.com/ElrondDevGuild/buildo-begins#available-commands-for-buildo-for-now-there-will-be-more)

Using one of them, you must provide the seed phrase (your 24 words) in a row with spaces between them. Also, please remember, don't show them to anyone.

In return, you will get the wallet PEM file. Keep it and remember the path to it. For the wallet part, we have all already. We have the file that will help us sign all transactions. Remember that the PEM file should be derived from a seed phrase from a wallet from the devnet. Also, please fund the wallet using the devnet faucet, which you will find in the [devnet web wallet](https://devnet-wallet.elrond.com/).

Next, we will need a couple of helpers to simplify interactions with Elrond API. We could, of course, prepare our tools for that. For example, use Axios to make calls, etc. But there is a ready-to-use library called [@elrondnetwork/erdjs-network-providers](https://github.com/ElrondNetwork/elrond-sdk-erdjs-network-providers). We will use it for simplicity. But feel free to prepare your tools. See how in the docs: [Extending the Network Providers](https://docs.elrond.com/sdk-and-tools/erdjs/extending-erdjs/#extending-the-network-providers).

## Let's see some code

We won't write a complete NodeJS script here, but we will describe all the steps and link to such a CLI tool that was already built and does exactly that.

Let's assume that your NodeJS app is already initialized and you installed the two mentioned above libraries, the [@elrondnetwork/erdjs](https://www.npmjs.com/package/@elrondnetwork/erdjs) and [@elrondnetwork/erdjs-network-providers](https://www.npmjs.com/package/@elrondnetwork/erdjs-network-providers). Oh btw, we will also need the third one [@elrondnetwork/erdjs-walletcore](https://www.npmjs.com/package/@elrondnetwork/erdjs-walletcore). More about it later in the article.

The next steps would be: 

1. Read the walletKey.pem file
2. Initialize Network Provider 
3. Initialize Account
4. Synchronize the account with the network using the Network Provider
5. Initialize UserSigner

Let's see what it looks like when it comes to code. You will also find the links to the real use case in [Buildo Begins](https://github.com/ElrondDevGuild/buildo-begins) library.

## Read the walletKey.pem file

You can do this in many different ways. Here we will do this like that: 

```typescript
export const getFileContents = (
  relativeFilePath: string,
  options: { isJSON?: boolean; noExitOnError?: boolean }
) => {
  const isJSON = options.isJSON === undefined ? true : options.isJSON;
  const noExitOnError =
    options.noExitOnError === undefined ? false : options.noExitOnError;

  const filePath = `${baseDir}/${relativeFilePath}`;

  try {
    accessSync(filePath, constants.R_OK | constants.W_OK);
  } catch (err) {
    if (!noExitOnError) {
      console.error(`There is no ${relativeFilePath}!`);
      exit(9);
    } else {
      return undefined;
    }
  }

  const rawFile = readFileSync(filePath);
  const fileString = rawFile.toString('utf8');

  if (isJSON) {
    return JSON.parse(fileString);
  }
  return fileString;
};
```
You will find the real example in the Buildo Begins library: https://github.com/ElrondDevGuild/buildo-begins.

We can then use the file contents across the app.

## Initialize Network Provider

For that we will use [@elrondnetwork/erdjs-network-providers](https://github.com/ElrondNetwork/elrond-sdk-erdjs-network-providers). 

```typescript
import { ApiNetworkProvider } from '@elrondnetwork/erdjs-network-providers';

export const getProvider = () => {
  return new ApiNetworkProvider('https://devnet-api.elrond.com', {
    timeout: 10000,
  });
};
```
Here simplified, and you will find the real example in the Buildo Begins library: https://github.com/ElrondDevGuild/buildo-begins.

The provider will be used wherever we need to interact with the Elrond API. It can be the public endpoint as in the example, but it is always advised to use a custom one—yours or from third-party services.

## Initialize Account

The Account instance will be used to synchronize and track the user transactions. What we need to do here is to initialize it. We will read the public address from our walletKey.pem file and initialize the Account.

For that we will need another erdjs library: [@elrondnetwork/erdjs-walletcore](https://www.npmjs.com/package/@elrondnetwork/erdjs-walletcore).

```typescript
import { parseUserKey } from '@elrondnetwork/erdjs-walletcore';

export const prepareUserAccount = async (walletPemKey: string) => {
  const userKey = parseUserKey(walletPemKey);
  const address = userKey.generatePublicKey().toAddress();
  return new Account(address);
};
```

You will find the real example in the Buildo Begins library: https://github.com/ElrondDevGuild/buildo-begins.

## Synchronize the account with the network using the Network Provider

To be ready to work, we need to synchronize our Account instance with the current state on the network. To do that, we will use the previously initialized network provider. And we will also get the user account using the previous function.

```typescript
(...)
const provider = getProvider();

const userAccount = await prepareUserAccount(walletPemKey);
const userAccountOnNetwork = await provider.getAccount(userAccount.address);
userAccount.update(userAccountOnNetwork);
(...)
```
This way, we are almost ready to issue our ESDT token. The only missing part is UserSigner.

## Initialize UserSigner

UserSigner is a helper class that allows signing the transactions using the walletKey.pem file. Here we will also need the [@elrondnetwork/erdjs-walletcore](https://www.npmjs.com/package/@elrondnetwork/erdjs-walletcore) library.

```typescript
import { UserSigner } from '@elrondnetwork/erdjs-walletcore';

export const prepareUserSigner = (walletPemKey: string) => {
  return UserSigner.fromPem(walletPemKey);
};
```

As you can see we use the `UserSigner.fromPem` function to create the signer using our PEM file. It can do the same using the JSON file and password. Check how it looks here: [userSigner.ts](https://github.com/ElrondNetwork/elrond-sdk-erdjs-walletcore/blob/main/src/userSigner.ts).

## Real-life example and next steps

With the erdjs setup now, we are ready to go. Here you will find fully functional ESDT issue token operations: [issue-esdt.ts](https://github.com/ElrondDevGuild/buildo-begins/blob/f661798936be9e5fdc521fbb25cac5c93b57617a/src/esdt/issue-esdt.ts).

The example comes from the Buildo Begins library. In the following steps, you will see how it is built. All previous actions are included in the `setup` function in the Buildo Begins example. Now, let's see the next steps.

We will omit the input operations like getting required data, token ticker, name, initial supply, decimal places, and properties. In Buildo Begins, a CLI tool, there is a library that helps get the inputs from the user. But let's assume we don't have to worry about it now. You can always check the example linked above and copy the code responsible for that.

Here we will also split the following operations into a couple of steps: 

1. Prepare the payment instance
2. Prepare transaction arguments
3. Prepare transaction payload
4. Prepare transaction to sign
5. Sign and send the transaction
6. Check the status of the transaction

## Prepare the payment instance

Each issuance of the ESDT token on the Elrond blockchain costs 0.05 EGLD. This is a predefined cost. But don't confuse it. This is the cost of issuing, not the fee when you want to buy or sell some amount.

To properly prepare and send the amount, we need to use a `TokenPayment` from erdjs.

```typescript
import { TokenPayment } from '@elrondnetwork/erdjs';
(...)
const payment = TokenPayment.egldFromAmount('0.05');
(...)
```
## Prepare transaction arguments

Then we need to prepare the arguments. The arguments previously mentioned are: 

 - token name
 - token ticker
 - initial supply
 - number of decimals
 - properties

The arguments are put into the array of `TypedValue` items. Erdjs provides helper classes to keep strong typings for each type of argument. This is required to prepare the payload for the transactions correctly. So below, you will find an example.

```typescript
import {
  BytesValue,
  U32Value,
  BigUIntValue,
  TypedValue,
} from '@elrondnetwork/erdjs';
import Bignumber from 'bignumber.js';
(...)
const args: TypedValue[] = [
  BytesValue.fromUTF8(name),
  BytesValue.fromUTF8(ticker),
  new BigUIntValue(new Bignumber(initialSupply)),
  new U32Value(numberOfDecimals),
];
(...)
```

The strings like name and ticker can be encoded as bytes using `BytesValue.fromUTF8`. The initial supply is a `BigUIntValue` from BigNumber. And the number of decimals is `U32Value`. What's left? Right the properties. When issuing the token, you can set predefined ESDT token properties to true or false. You will find more info about them here: [Configuration properties of an ESDT token](https://docs.elrond.com/developers/esdt-tokens/#configuration-properties-of-an-esdt-token). Ok, let's see how we can add them to the args array. 

```typescript
import { BytesValue } from '@elrondnetwork/erdjs';
(...)
args.push(BytesValue.fromUTF8(property));
args.push(BytesValue.fromUTF8(propertyEnabled.toString()));
(...)
```
So here, the `property` is the property's name, and `propertyEnabled` is a boolean, true for enabled and false for disabled. You can loop through all properties and push them to the args array. So, first the name, then the boolean flag, then the next property name and the boolean flag. Booleans as strings encoded to BytesValue.

This way, we will have complete arguments payload in the `args` variable. Let's see what's next.

For more data types, you can check the erdjs source code here: [typesystem](https://github.com/ElrondNetwork/elrond-sdk-erdjs/tree/main/src/smartcontracts/typesystem).

## Prepare transaction payload

There are a lot of different ways to prepare the transaction data payload. In our case, we can use `ContractCallPayloadBuilder`.

```typescript
import { ContractCallPayloadBuilder, ContractFunction } from '@elrondnetwork/erdjs';
(...)
 const data = new ContractCallPayloadBuilder()
  .setFunction(new ContractFunction('issue'))
  .setArgs(args)
  .build();
```

As you can see, we are passing our `args` array here, but we also need to pass the smart contract function to call. Here it is a built-in `issue` function. Again you will find all the information about ESDT in the docs here: [Issuance of fungible ESDT tokens](https://docs.elrond.com/developers/esdt-tokens/#issuance-of-fungible-esdt-tokens).

Btw. you can find more helpers for payload creation here: [TransactionPayload](https://github.com/ElrondNetwork/elrond-sdk-erdjs/blob/main/src/transactionPayload.ts) and [Smart contract transaction payload builders](https://github.com/ElrondNetwork/elrond-sdk-erdjs/blob/main/src/smartcontracts/transactionPayloadBuilders.ts).

With our `data` payload, we are ready to construct the final form of the transaction to sign. 

## Prepare transaction to sign

Here we will also use a couple of helper classes. The main is the `Transaction`.

```typescript
import { Transaction, Address } from '@elrondnetwork/erdjs';
(...)
const tx = new Transaction({
  data: data,
  gasLimit: 60000000,
  receiver: new Address('erd1qqqqqqqqqqqqqqqpqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqzllls8a5w6u'),
  value: payment,
  chainID: 'D',
});
```

There is a couple of essential parts. First of all, the `data` is our previously created payload. Then we have the `payment`. Also, our previously created payment instance. Remember? The 0.05 EGLD for issuance. 

The new things here are the gas limit which should always be passed with the transaction. In this case, it is simple, the gas limit will always be the same in such a transaction, and it is 60000000. But in custom transactions, you would need to calculate it. For simplicity, you can always set the max limit, which is 600000000 and then check in the explorer how much was used and change it. There are also other ways of doing that, but let's leave them for now. You will read more about it here: [Gas and Fees](https://docs.elrond.com/developers/gas-and-fees/overview/).

Then we also have the chain type, where you should provide on which chain you operate. `D` is for the devnet, `T` is for the testnet and `1` is for the mainnet. We will use the `D` here.

The last is the built-in address mentioned already in this article. It is a unique address that you should use for issuing ESDTs. Again, you will find all these informations in the official docs.

Ok, so far, we have prepared the payment, arguments, and data payload, and we have also prepared the transaction. The following steps would be to sign and send the transaction.

## Sign and send the transaction

We will use the previously prepared `signer` and network provider for signing and sending. All are also described at the beginning of the article. Remember? The `setup` function from the Buildo Begins.

```typescript
(...)
tx.setNonce(account.nonce);
account.incrementNonce();
signer.sign(tx);
await provider.sendTransaction(tx);
```

Our previously created transaction is `tx` here. Check above.

Besides the `signer` and `provider`, we also have the `account` here, described at the beginning of the article - section 'Initialize Account'.

We need to set the nonce for the transaction based on the user account nonce, and then we need to increment the nonce. This operation is for every transaction we make with the same user account.

Then finally, we can sign the transaction using the `signer.sign` function, and we need to pass our final `tx` instance.

Then we can use `provider` to send a signed transaction. What it will do is it will use the previously defined API endpoint and send the transaction with all required data and signature.

Let's see how we can listen for transaction status.

## Check the status of the transaction

We've sent the transaction, but of course, all is asynchronous, so we need to have a way to listen for changes and the possibility to catch the status of the transaction or errors.

For this, we will use the `TransactionWatcher` from erdjs SDK. 

```typescript
import { TransactionWatcher } from '@elrondnetwork/erdjs';
(...)
const watcher = new TransactionWatcher(provider);
const transactionOnNetwork = await watcher.awaitCompleted(tx);

const txHash = transactionOnNetwork.hash;
const txStatus = transactionOnNetwork.status;
```

As you can see, we still use our network `provider` here to be able to listen for changes. We also use `tx` - the instance of our transaction. `watcher.awaitCompleted(tx);` will keep polling the network to check if any changes occurred related to this particular transaction. Then it will return the state of the transaction or throw an error.

You can read the status directly. You can also get the hash of the transaction at this stage. Now the whole process should be finished, and you should be able to use the Elrond explorer to see the entire history using the transaction hash.

## How to do the same but on the mainnet

For the mainnet, you would need to change three things:
1. You need the walletKey.pem file derived from your mainnet wallet seed phrase. It should also have some EGLD already.

2. You need to provide a different API endpoint for the network provider, so, for example:

```typescript
import { ApiNetworkProvider } from '@elrondnetwork/erdjs-network-providers';

export const getProvider = () => {
  return new ApiNetworkProvider('https://api.elrond.com', {
    timeout: 10000,
  });
};
```

3. And then, you would also need to use the proper chain id when creating the transaction, for example: 

```typescript
import { Transaction, Address } from '@elrondnetwork/erdjs';
(...)
const tx = new Transaction({
  data: data,
  gasLimit: 60000000,
  receiver: new Address('erd1qqqqqqqqqqqqqqqpqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqzllls8a5w6u'),
  value: payment,
  chainID: '1',
});
```

See? The `1` instead of the `D` as previously.

## Summary

The flow here is very similar for each transaction. The only differences will be in preparing the data payload, arguments, and sometimes slight differences in transaction instances. All is described in the docs, so you would only need to do the same using helper classes from erdjs SDK.

Here we issued the ESDT token. Of course, this wasn't a complete application, but there is one, and it is open source, so you can check the code by yourself and even copy what you need. You will find it here: https://github.com/ElrondDevGuild/buildo-begins, and this particular functionality is here: [issue-esdt.ts](https://github.com/ElrondDevGuild/buildo-begins/blob/main/src/esdt/issue-esdt.ts).

If you are curious about how it works. You can use the Buildo Begins library by installing it globally: `npm install buildo-begins -g`. Check the readme.md in the repository for more documentation on it.

If you liked the article, there would be more like this one, maybe focused more on token operation instead of the erdjs required tools. The plan is to describe all that will land in the Buildo Begins library.

[julian.io](https://www.julian.io)
