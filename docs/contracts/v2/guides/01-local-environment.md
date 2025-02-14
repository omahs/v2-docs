---
id: "local-environment"
sidebar_position: 1
title: "Configure Your Local Environment"
---

In this guide, we will go through the steps to set up a local development environment for building on-chain integrations
with Sablier. We will use Foundry to install Sablier as a dependency, and run a few simple tests.

At the end, you’ll have a development environment set up that you can use to build the rest of the examples under
"Guides", or start your own integration project.

## Pre-requisites

You will need the following software on your machine:

- [Git](https://git-scm.com/downloads)
- [Foundry](https://github.com/foundry-rs/foundry)
- [Node.js](https://nodejs.org/en/download)
- [Pnpm](https://pnpm.io)

In addition, familiarity with [Ethereum](https://ethereum.org/) and [Solidity](https://soliditylang.org/) is requisite.

## Quick start

We put together a template repository that you can use to get started quickly. This repository features a basic project
structure, pre-configured Sablier imports, and a selection of sample contracts and tests.

:::tip

Make sure you are using the latest version of Foundry by running `foundryup`.

:::

To install the template, simply execute the following commands:

```shell
$ mkdir sablier-v2-integration-template
$ cd sablier-v2-integration-template
$ forge init --template sablier-labs/sablier-v2-integration-template
$ pnpm install
```

Then, hop to the `Run a Fork Test` section to complete your set up and start developing.

## Start from scratch

Foundry is a popular development toolkit for Ethereum projects, which we have used to build the Sablier Protocol. For
the purposes of this guide, Foundry will provide us with the tooling needed to compile and test our contracts.

Let's use this command to spin up a new Foundry project:

```shell
$ forge init my-project
$ cd my-project
```

:::note

You might notice that the CLI is `forge` rather than `foundry`. This is because Foundry is a toolkit, and `forge` is
just one of the tools that comes with it.

:::

Once the initialization completes, take a look around at what got set up:

```tree
├── foundry.toml
├── lib
├── script
├── src
└── test
```

The folder structure should be intuitive:

- `src` is where you’ll write Solidity contracts
- `test` is where you’ll write tests (also in Solidity)
- `script` is where you'll write scripts to perform actions like deploying contracts (you guessed it, in Solidity)
- `foundry.toml` is where you can configure your Foundry settings, which we will leave as is in this guide

Let's install the Sablier contracts:

```shell
$ forge install sablier-labs/v2-core sablier-labs/v2-periphery
```

Under the hood, Foundry will clone the Sablier repositories and install the contracts as git submodules.

Finally, let's remap the `@sablier/v2-core` and `@sablier/v2-periphery` paths to point to the installed contracts. This
step is required so that the Solidity compiler can find the Sablier contracts when you import them:

```shell
$ echo "@sablier/v2-core=lib/v2-core/src" >> remappings.txt
$ echo "@sablier/v2-periphery=lib/v2-periphery/src" >> remappings.txt
```

That's it! You should now have a functional development environment to start building on-chain Sablier integrations.
Let's run a quick test to confirm everything is set up properly.

## Sample contract

Delete the `src/Counter.sol` and `test/Counter.t.sol` files generated by Forge, and create two new files:
`src/StreamCreator.sol` and `test/StreamCreator.t.sol`.

Paste the following code into `src/StreamCreator.sol` (a detailed explanation of this contract can be found
[here](/contracts/v2/guides/create-stream/lockup-linear)):

```solidity reference title="Lockup Linear Stream Creator"
https://github.com/sablier-labs/examples/blob/main/v2/core/LockupLinearStreamCreator.sol
```

Let's use Forge to compile this contract:

```shell
$ forge build
```

If the contract was compiled correctly, you should see this message:

```text
[⠢] Compiling...
[⠰] Compiling { number } files with { compiler }
[⠒] Solc { compiler } finished in { time }
Compiler run successful
```

:::info

The minimum Solidity version supported by the Sablier contracts is v0.8.13.

:::

## Run a fork test

Foundry offers native support for running tests against a fork of Ethereum Mainnet, which is useful when building and
testing integrations with on-chain protocols like Sablier. In practice, this enables you to access all Sablier contracts
deployed on Ethereum, and use them for testing your integration.

As a prerequisite, you will need an RPC that supports forking. A good solution for this is
[Alchemy](https://alchemy.com/), as it includes forking in its free tier plan.

Once you have obtained your RPC, you can proceed to run the following test:

```solidity reference title="Stream Creator Test"
https://github.com/sablier-labs/examples/blob/main/v2/core/LockupLinearStreamCreator.t.sol
```

You can run the test using Forge:

```shell
$ forge test
```

If the test passed, you should see a message like this:

```text
Running 1 test for test/StreamCreator.t.sol:StreamCreatorTest
[PASS] test_CreateStream() (gas: { gasValue })
Test result: ok. 1 passed; 0 failed; finished in { time }
```

## Next steps

Congratulations! Your environment is now configured, and you are prepared to start building. Explore the guides section
to discover various Sablier features available for integration. Remember to include all contracts (`.sol` files) in the
`src` folder and their corresponding tests in the `test` folder.

As far as Foundry is concerned, there is much more to uncover. If you want to learn more about it, check out the
[Foundry Book](https://book.getfoundry.sh/), which contains numerous examples and tutorials. A deep understanding of
Foundry will enable you to create more sophisticated integrations with Sablier.
