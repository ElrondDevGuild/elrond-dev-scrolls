---
title: Linux environment for development
description: How to prepare the Linux environment for Elrond development
---

# Linux environment for development

The configuration is relatively straightforward, but usually, there are problems with that. It all depends on the Linux distribution and version.

If you don't want to read all, please check the walkthrough video below.

{% embed url="https://youtu.be/Uju4B-CCAJw" %}

### What kind of operating system is the best?

I usually work on Linux, and I think this is the best choice overall when you are a developer. The best for me is Ubuntu, but it can be any distribution. You should also be good with Mac OS, but the configuration can differ slightly. So this is why I will show it on Linux.

### What tooling will be required?

* Nodejs environment (although it isn't strictly required for only smart contracts, it will be crucial for all other toolings like erdjs)
* Python (at least 3.8) - required by the erdpy tool
* VSCode (required because of VSCode Elrond IDE extension, but you can still work with only erdpy, rust, etc.)
* Optionally Rust Analyzer VSCode extension, which is very helpful.

The video will also show how to deploy and interact with a simple, smart contract.

### First steps.

Let's install the [NVM](https://github.com/nvm-sh/nvm) tool to help with Nodejs versions management. You could install Node in many different ways, but I found that NVM works best for me, and it removes all problems related to permissions, etc.

You can install it by:

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

Then you can export a couple of env variables. You will find them after the script finishes working. Just copy them and paste them into the terminal, then press enter. Check the video. However, I had it installed already when recording. Restart the terminal after installing it.

You can install Node in a chosen version by:

```
nvm install 16.16.0
```

The nvm will be a global command in your system. Then you can switch between versions if needed by `nvm use <version>`.

### Install git.

Git is also not required in this particular tutorial, but I can imagine working without it. You will need to clone repositories and maintain your own. In the video, it is required to clone the example smart contract.

You can install it using:

```
sudo apt install git-all
```

You will find more information here: [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

### Install Python and erdpy.

Erdpy is a Python tool that can serve as a CLI or module in a Python environment. It is a perfect tool for any interactions with the Elrond blockchain. We will use it to deploy and interact with the smart contract.

To install the erdpy, let's first install the Python environment. You can do this in three steps:

```bash
sudo apt install software-properties-common
```

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
```

(although you will probably install it without it. From default Ubuntu repositories. It is here to be sure that it will work for sure.)

```bash
sudo apt install python3
```

If you plan to run smart contracts written in C, you will also need `sudo apt install libncurses5`.

Then we will install `erdpy.` First, we need to download the erdpy-up script and then run it:

```bash
wget -O erdpy-up.py https://raw.githubusercontent.com/ElrondNetwork/elrond-sdk-erdpy/master/erdpy-up.py
```

```bash
python3 erdpy-up.py
```

### Install VSCode

Visual Studio Code is one of many code editors and is most used nowadays. This is why the Elrond IDE extension is written for it. I guess.

To download the VSCode for Linux, go to: [code.visualstudio.com](https://code.visualstudio.com/) and download the .deb package. Then install it using the Ubuntu software install app, or you can also install it using terminal `sudo dpkg -i DEB_PACKAGE`.

When you have already installed the VSCode, you can use its UI and install two extensions. One is the Elrond IDE extension, which you will find using the VSCode extensions search. Then you can install the Rust Analyzer. Don't worry if it won't work the first time. It requires Rust tooling as dependencies, and the Elrond IDE extension will install all missing parts with the first smart contract build. You can build the smart contract by right-clicking it on the list and choosing 'Build smart contract'.

### Clone the simple, smart contract for testing.

You can clone the Piggybank smart contract for testing. I also did that on the video, so check it out. You will find the smart contract here: [elrond-simple-sc](https://github.com/ElrondDevGuild/elrond-simple-sc).

Clone it and open the VSCode. In the Elrond tab, you should now see the smart contract. You can build it by right-clicking on it and choosing 'Build smart contract'.

In the Readme file in the repository, you will find the erdpy commands to interact with the smart contract. You will also see that on the video.

### Summary

It was a quick walkthrough on how to start writing the smart contracts on the Elrond network in the context of the configuration of the development environment. You can do similar steps on Mac OS. Windows isn't officially supported, but you should be able to do the same using [Windows Linux Subsystem](https://docs.microsoft.com/en-us/windows/wsl/install).

Let's sum up the steps:

1. Not mandatory but will be needed: NodeJS, Git configuration
2. Required Python setup
3. Required erdpy installation
4. Not mandatory, but it is much simpler to work with VSCode and Elrond IDE extension
5. The example smart contract is a simple Piggybank functionality, where you lock your EGLD for some time. After that, you can do the payout.

Check it out: [github.com/ElrondDevGuild/elrond-simple-sc](https://github.com/ElrondDevGuild/elrond-simple-sc) Soon there will be more documentation and other ways of interaction, like using Typescript-based erdjs SDK or dApp.

julian.io
