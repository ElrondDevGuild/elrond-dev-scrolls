---
description: Code snippet was written on erdjs v10.2.5
---

# Use PEM file wallet

This is a practical example of loading a PEM file wallet and making it ready to be used in your application to sign & send transactions. 
Remember that this is used mainly in CLI tools. In web applications, you can use [Signing providers](https://docs.elrond.com/sdk-and-tools/erdjs/erdjs/). We will also provide some content about that.

In order to sign & send a transaction, we'll need 3 components:

* UserSigner - to sign the transaction
* Account - this will be the account sending the transaction
* ApiNetworkProvider - this is will basically tell the chain where the transaction will be sent (optional, but for a quick start recommended)

Also, we'll need 3 packages in order to implement this

```
npm i @elrondnetwork/erdjs @elrondnetwork/erdjs-network-providers @elrondnetwork/erdjs-walletcore
```

## Get the UserSigner

```
import { UserSigner } from '@elrondnetwork/erdjs-walletcore';

export const prepareUserSigner = (walletPemKey: string) => {
  return UserSigner.fromPem(walletPemKey);
};
```

Now,  we need a method that will read the PEM key from the PEM file

```
export const getFileContents = (relativeFilePath: string, options: { isJSON?: boolean; noExitOnError?: boolean }) => {
  const isJSON = options.isJSON === undefined ? true : options.isJSON;
  const noExitOnError = options.noExitOnError === undefined ? false : options.noExitOnError;

  const filePath = `${baseDir}/${relativeFilePath}`;

  try {
    accessSync(filePath);
  } catch (err) {
    if (!noExitOnError) {
      console.error(`There is no ${relativeFilePath}!`);
      exit(9);
    } else {
      return undefined;
    }
  }

  const rawFile = readFileSync(filePath);
  const fileString = rawFile.toString("utf8");

  if (isJSON) {
    return JSON.parse(fileString);
  }
  return fileString;
};
```

Now, we can combine those 2 functions to get our UserSigner

```
const walletPemKey = getFileContents('wallet.pem', { isJSON: false });

const signer = prepareUserSigner(walletPemKey);
```

## Get the ApiNetworkProvider

In order to create an ApiNetworkProvider, we need to know the chain will use. Let's assume will use DEVNET.

```
export const getProvider = () => {
  return new ApiNetworkProvider('https://devnet-api.elrond.com', {
    timeout: 10000,
  });
};
```

The URL for other chains are:

* testnet: https://testnet-api.elrond.com
* devnet: https://devnet-api.elrond.com
* mainnet: https://api.elrond.com

These are public endpoints, and using them in production-ready apps isn't recommended. You can always use your custom architecture or 3rd party services. Then you would need to switch the endpoint or, even better, create your API provider. You can read more about it here: [Extending the Network Providers](https://docs.elrond.com/sdk-and-tools/erdjs/extending-erdjs/).

## Get the Account

We can also use that `walletPemKey` we got earlier to create an Account object

```
import { parseUserKey } from '@elrondnetwork/erdjs-walletcore';
import { Account } from '@elrondnetwork/erdjs';

export const prepareUserAccount = async (walletPemKey: string) => {
  const userKey = parseUserKey(walletPemKey);
  const address = userKey.generatePublicKey().toAddress();
  return new Account(address);
};
```

Now, we can put all the above together and create a function that we can reuse in our app every time we need to sign & send a transaction

```
export const publicEndpointSetup = async () => {
  const walletPemKey = getFileContents('wallet.pem', { isJSON: false });

  const provider = getProvider();

  const userAccount = await prepareUserAccount(walletPemKey);
  const userAccountOnNetwork = await provider.getAccount(userAccount.address);
  userAccount.update(userAccountOnNetwork);

  const signer = prepareUserSigner(walletPemKey);

  return {
    signer,
    userAccount,
    provider,
  };
};
```

_Code snippets taken & adapted from_ [_buildo-begins_](https://github.com/ElrondDevGuild/buildo-begins/blob/main/src/utils.ts)_._

## Generate PEM file wallet

One simple way to generate a PEM file wallet is using [erdpy](https://docs.elrond.com/sdk-and-tools/erdpy/installing-erdpy/). In a terminal, run:

```
erdpy wallet new --pem --output-path wallet
```

Remember to always fund your newly created wallet, for example, using the Elrond Web Wallet, where you can also log in using your PEM file.

### Derive PEM file

If you already have a wallet, you can also derive your PEM from the seed phrase using both erdpy and buildo-begins lib (based on erdjs) mentioned above:
- with erdpy: `erdpy --verbose wallet derive ./walletKey.pem --mnemonic`
- with buildo-begins `buildo-begins derive-pem`
In both cases, you will need to provide the seed phrase like `words here some mnemonic words more words et cetera ...`
