---
title: Linux environment for development
description: How to prepare the Linux environment for MultiversX development
---

# Linux environment for development

The configuration is relatively straightforward, but usually, there are problems with that. It all depends on the Linux distribution and version.

If you don't want to read all, please check the walkthrough video on MultiversX VSCode IDE extension, which is the most straightforward way of preparing the environment.

{% embed url="https://youtu.be/y0beoihLppA" %}

### What kind of operating system is the best?

I usually work on Linux, and this is the best choice overall when you are a developer. The best for me is Ubuntu, but it can be any distribution. You should also be good with Mac OS, but the configuration can differ slightly. So this is why I will show it on Linux.

### What tooling will be required?

* Nodejs environment (although it isn't strictly required for only smart contracts, it will be crucial for all other toolings like JS SDK)
* Python (at least 3.8) - required by the mxpy tool (should be shipped with newest Ubuntu versions)
* VSCode (required because of VSCode MultiversX IDE extension, but you can still work with only mxpy, rust, etc.)
* Optionally Rust Analyzer VSCode extension, which is very helpful.

The video will also show how to deploy and interact with a simple, smart contract.

### First steps.

Let's install the [NVM](https://github.com/nvm-sh/nvm) tool to help with Nodejs version management. You could install Node in many different ways, but NVM works best for me, and it removes all problems related to permissions, etc.

You can install it by (make sure you have curl installed `sudo apt install curl`):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

Restart the terminal after installing it.

You can install Node in a chosen version by:

```
nvm install 18.13.0
```

The nvm will be a global command in your system. Then you can switch between versions if needed by `nvm use <version>`.

### Install Git.

Git is also not required in this particular tutorial, but I can imagine working without it. You will need to clone repositories and maintain your own. In the video, it is required to clone the example smart contract.

You can install it using the following:

```
sudo apt install git-all
```

You will find more information here: [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

### Install VSCode

Visual Studio Code is one of many code editors and is most used nowadays. This is why the MultiversX IDE extension is written for it. I guess.

To download the VSCode for Linux, go to [code.visualstudio.com](https://code.visualstudio.com/) and download the .deb package. Then install it using the Ubuntu software install app, or install it using terminal `sudo dpkg -i DEB_PACKAGE`.

When you have already installed the VSCode, you can use its UI and install two extensions. One is the MultiversX IDE extension, which you will find using the VSCode extensions search. Then you can install the Rust Analyzer. Don't worry if it won't work the first time. It requires Rust tooling as dependencies, and the MultiversX IDE extension will install all missing parts with the first smart contract build. Check how to configure VSCode workspace settings for Rust Analyzer [here](https://github.com/multiversx/mx-ide-vscode/releases/tag/v0.18.0).

By typing `Ctrl + Shift + P`, you will open the prompt where you need to write `MultiversX` and choose to set up the workspace. It will install `mxpy` and also all required `Rust` tools.

You can build the smart contract by right-clicking it on the list and choosing 'Build smart contract'.

### Install Python (if you don't have it, most Ubuntu distributions have it already).

Mxpy is a Python tool that can serve as a CLI or module in a Python environment. It is a perfect tool for any interactions with the MultiversX blockchain. We will use it to deploy and interact with the smart contract.

To install the mxpy, let's first install the Python environment. You can do this in three steps:

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


### Optionally install mxpy separately if you don't want to use VSCode

To install `mxpy` first, we need to download the mxpy-up script and then run it:

```bash
wget -O mxpy-up.py https://raw.githubusercontent.com/multiversx/mx-sdk-py-cli/main/mxpy-up.py
```

```bash
python3 mxpy-up.py
```

**Using mxpy standalone without VSCode is possible, but VSCode IDE will simplify things.**

### Entry in .bashrc

If you use bash and after installing mxpy you still can't access it, make sure that you have the `export PATH="${HOME}/multiversx-sdk:${PATH}"` entry in your `.bashrc` file. It should be located in your user's home directory.

### Clone the simple, smart contract for testing.

You can clone the Piggybank smart contract for testing. I also did that on the video, so check it out. You will find the smart contract here: [multiversx-simple-sc](https://github.com/xdevguild/multiversx-simple-sc).

Clone it and open the VSCode. In the MultiversX tab, you should now see the smart contract. You can build it by right-clicking on it and choosing 'Build smart contract'.

In the Readme file in the repository, you will find the mxpy commands to interact with the smart contract. You will also see that on the video.

###  Build the smart contract and interact

There are interaction snippets in the example smart contract repository. You can use them to interact with it, deploy and upgrade. Check the video.

### Troubleshooting

- you might be missing the `build-essential` library with the fresh Ubuntu. You can install it by `sudo apt install build-essential`. Without it, your builds will probably fail
- when you are preparing the workspace for the first time, remember that you will need to restart all your terminals between installing mxpy and Rust tools, be careful because you can have a couple of them so close all of them

### Summary

It was a quick walkthrough on how to start writing the smart contracts on the MultiversX network in the context of the configuration of the development environment. You can do similar steps on Mac OS. Windows isn't officially supported, but you should be able to do the same using [Windows Linux Subsystem](https://docs.microsoft.com/en-us/windows/wsl/install).

Let's sum up the steps:

1. Not mandatory but will be needed: NodeJS, Git configuration
2. Required Python setup (should be already there with the newest Ubuntu)
3. Required mxpy installation (standalone or with VSCode IDE extension)
4. Not mandatory, but it is much simpler to work with VSCode and MultiversX IDE extension
5. The example smart contract is a simple Piggybank functionality where you lock your EGLD for some time. After that, you can do the payout.

Check it out: [github.com/xdevguild/multiversx-simple-sc](https://github.com/xdevguild/multiversx-simple-sc) Soon, there will be more documentation and other ways of interaction, like using Typescript-based SDK or dApp.

julian.io
