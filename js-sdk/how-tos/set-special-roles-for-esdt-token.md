---
title: Set special roles for ESDT - for example, using the JS SDK
description: Set special roles for ESDT tokens using JS SDK based on a real-life example
---

This part assumes that you went through the first article. Where you should've learned more about how to prepare the JS SDK and how to set up the user account and user signer to be able to sign transactions. If you haven't read it, please do it first. You'll find it here: [Setup the JS SDK workflow and issue an ESDT token in the NodeJS environment](https://elrond-dev-guild.gitbook.io/scrolls/js-sdk/how-tos/setup-js-adk-and-issue-esdt-token).

## What are the ESDT special roles

The ESDT tokens can have two special roles. You would need to define them after the issuance of the ESDT token. Again, please check the linked above article on how to issue an ESDT token.

Ok, let's check what kind of roles we can define, and then we will jump into the code. 
- `ESDTRoleLocalBurn`: an address with this role can burn tokens
- `ESDTRoleLocalMint`: an address with this role can mint new tokens

Important: To be able to manage these roles, the token must have the `canAddSpecialRoles` property. Please read more about the properties in the previous article or here: [Configuration properties of an ESDT token](https://docs.multiversx.com/tokens/esdt-tokens/#configuration-properties-of-an-esdt-token).

To sum up this part. You can issue the ESDT token and assign special roles to it. You will find all of the roles above. The roles are required to control the supply of the token. You can mint more or burn some of the tokens. The supply will change.

Now, let's see how to assign the roles using the JS SDK. 

As mentioned above, I assume that you have your Nodejs setup ready. You have the JS SDK UserSigner and account prepared. If not, please read the first article.

I will also use the [Buildo Begins](https://github.com/xdevguild/buildo-begins) library as an example here. The same way as in the previous article.

## ESDT special roles and JS SDK

The definition of required transaction is here: [Set special role](https://docs.multiversx.com/tokens/esdt-tokens/#set-special-role). So please read it and then we can open the code example and see how to implement it in the JS SDK.

Let's jump into the Buildo Begins code: [set-special-roles-esdt.ts](https://github.com/xdevguild/buildo-begins/blob/main/src/esdt/toggle-special-roles-esdt.ts). If you read through the first article you will be probably familiar with the approach there. The setup helper function prepares `userAccount`, `provider`, and `signer`. 

We can start preparing our transaction's arguments and data payload. Here is what it looks like:

```typescript
const args: TypedValue[] = [
  BytesValue.fromUTF8(ticker),
  new AddressValue(new Address(address.trim())),
];

for (const role of esdtTokenSpecialRoles) {
  if (specialRoles.includes(role)) {
    args.push(BytesValue.fromUTF8(role));
  }
}

const data = new ContractCallPayloadBuilder()
  .setFunction(new ContractFunction('setSpecialRole'))
  .setArgs(args)
  .build();

const tx = new Transaction({
  data,
  gasLimit: esdtOpertationsGasLimit,
  receiver: new Address(builtInEsdtSC.trim()),
  value: 0,
  chainID: shortChainId[chain],
});
```

First, we need to provide the token's ticker and manager address. We need to prepare an array of arguments. It looks like that: 

```typescript
const args: TypedValue[] = [
  BytesValue.fromUTF8(ticker),
  new AddressValue(new Address(address.trim())),
];
```

`BytesValue`, `AddressValue`, and `Address` all come from the `@multiversx/sdk-core` package and are important helpers that simplify communication between the JavaScript/Typescript and protocol.

Next, we need to provide all roles that we want to enable. In this example, we iterate through the list of roles and push to the array the ones we want to allow. We push to the same `args` array where we already have the ticker and address.

```typescript
 for (const role of esdtTokenSpecialRoles) {
  if (specialRoles.includes(role)) {
    args.push(BytesValue.fromUTF8(role));
  }
}
```

The role is provided as a bytes value, and we need to add its name.

Of course, these code blocks are copied without context, so please check the actual code linked above.

After we have prepared our arguments for the transaction, we can now organize the transaction payload. We will keep it under the `data` variable and use a couple of more JS helpers here.

```typescript
const data = new ContractCallPayloadBuilder()
  .setFunction(new ContractFunction('setSpecialRole'))
  .setArgs(args)
  .build();
```

We use the `ContractCallPayloadBuilder` from JS and we set the function which is called `setSpecialRole`. The reminder. It is predefined name for the function built into the protocol. More in the [official docs](https://docs.multiversx.com/tokens/esdt-tokens/#setting-and-unsetting-special-roles).

As you can see, we also set up a previously filled array of arguments.

And finally, we can build the payload to be readable by the protocol.

The next step is to prepare our transaction. The only missing parts are configuration values like gas limit, chain type, and receiver address. The receiver address, in this case, is a special built-in address: `erd1qqqqqqqqqqqqqqqpqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqzllls8a5w6u`.

The whole transaction instance looks like that:

```typescript
const tx = new Transaction({
  data,
  gasLimit: 60000000,
  receiver: new Address('erd1qqqqqqqqqqqqqqqpqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqzllls8a5w6u'),
  value: 0,
  chainID: 'D',
});
```
I filled in the values that are hidden behind the variables for simplicity. As you can see, we also pass here our `data` payload. The value to send should be set to 0 in this case.

With such a transaction, we can move to the following steps, where we will sign it and send it.

## Sign and send the transaction

In the Buildo Begins example, the sign and send operations are grouped in `commonTxOperations,` You can also read about it in the previous article.

What we are doing there is (tx is our transaction instance from above):

```typescript
tx.setNonce(account.nonce);
account.incrementNonce();
signer.sign(tx);

await provider.sendTransaction(tx);

const watcher = new TransactionWatcher(provider);
const transactionOnNetwork = await watcher.awaitCompleted(tx);

const txHash = transactionOnNetwork.hash;
const txStatus = transactionOnNetwork.status;
```

That's basically it. You will get the transaction hash and be able to check it in the explorer—the devnet explorer in this case, which you will find under: https://devnet-explorer.multiversx.com/.

Again, these are code examples taken out from the context, so please check the complete code example in Buildo Begins here: [set-special-roles-esdt.ts](https://github.com/xdevguild/buildo-begins/blob/main/src/esdt/toggle-special-roles-esdt.ts).

## Summary

The article was a continuation of the previous one. Here I focused mainly on the special roles assignment without going deeper into how to prepare user signer, account, etc., because all of that was explained previously. The following articles will also keep that convention. But please let me know what should be described in more detail.

As always, please check the [Buildo Begins](https://github.com/xdevguild/buildo-begins) library, where you will find real-life examples that work and do the job.

[julian.io](https://www.julian.io)
