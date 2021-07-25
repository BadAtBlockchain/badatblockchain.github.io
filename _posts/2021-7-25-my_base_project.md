---
layout: post
title: "My Base Project"
author: "Bad"
tags: misc
---

## My Base Project

### Quick Recap of My Process

One thing I love doing as a dev, is making my own life easier. Whilst attempting to spin up new projects to test my Solidity skills and practice building new contracts for different uses, I realised there were a few areas that definitely needed some optimising.

A quick recap of my development process was:

- Create basic project using truffle or simply just copy/pasting another project and gutting it

- Add the Harmony testnet information to a ".env" file

- Install the required npm modules (either manually or by creating the package file with required entries)

- Edit the contract and write a basic test (or whatever is required for the project)

- Attempt the test with the following commands:

  - ```
    truffle compile --all
    truffle migrate --network testnet --reset
    truffle test --network testnet
    ```

The core issues here are pretty obvious! These are:

- Inefficient project creation (manually copy/pasting and creating leads to easy human error)
- Extremely slow testing process due to the requirement of Harmony's testnet. Since starting my development process, the testnet has 'broken', specifically by going out a of sync. I don't know the true technical issues here, but requests time out, nothing migrates and test functions take AGES and sometimes just never even complete. A typical compile/migrate/test exercise can take 15 minutes. Which is pretty awful, especially when most developing/testing cycles are small changes and implementations.

### My New Process

To address these changes, I have changed my process to follow these steps:

- Clone my new base project that has been designed to efficiently set up at environment for development, requiring minimal (w.i.p) manual changes
- Run a single command to install and set up all required modules
- Edit contracts, tests and deployment scripts as required
- Run tests locally

The process appears similar, but let's break it down a little further and understand why it's much more efficient...

### The Base Project

So first of all, what is actually in this project when we first clone it?

- 'contracts' directory
- 'migrations' directory
- 'test' directory
- The usual '.env' file
- A 'truffle-config.js' file
- A 'package.json' file
- A range of files related to the new development environment and pipeline.

There isn't much to discuss about the directories, they all existed in my previous environment. It is worth noting that all tests are now written in TypeScript, not just JavaScript. For those that don't know, TypeScript (TS) is a derivative for JavaScript (JS). TS can include both TS and JS, where as JS can only interpret JS. As far as I am aware, TS is compiled where as JS is compiled with a JIT (just in time) compiler at runtime.

Now we are also using mocha to run all tests, a newly added file (with high importance) is the '.mocharc.json' file, found in the base directory of the project. When mocha is ran, this file is utilised:

```
{
    "extension": ["ts"],
    "spec": "./test/**/*.spec.ts",
    "require": "ts-node/register",
    "timeout": 30000
}
```

From the above, without over explaining, we can see tests need to be within the 'test' directory and should always end in '.spec.ts'. This can be changed to whatever you want, as long as it correlates with the test files themselves.

Looking into the 'package.json' file, a collection of scripts has been declared and added that control the flow of execution when running our yarn commands within the cli:

```
  "scripts": {
    "lint": "yarn prettier ./test/*.ts --check",
    "lint:fix": "yarn prettier ./test/*.ts --write",
    "clean": "rimraf ./build/",
    "precompile": "yarn clean",
    "compile": "waffle .waffle.json",
    "pretest": "yarn compile",
    "test": "mocha",
    "prepublishOnly": "yarn test"
  }
```

 When using this project, I tend to exclusively use the following commands during my development process:

``` 
yarn compile
yarn test
```

The above 2 commands alone improve my overall process and massively speeds up everything! Would recommend.

### From Cloning to Custom Project

So, now the project is cloned... what is actually required to get this code up and running? I mean, it depends what you want to do with it. Hypothetically, if you want to create and test a new currency token with this project, one would only really need to edit the main contract script within the contracts file, alter the test file to make sure it points to the compiled contract within the 'builds' directory (this won't exist until after compilation has occurred).

From a brief, high level perspective. That is literally it. By using the ERC20 contract from Open Zeppelin, deployment is easy and standard token behaviour is possible within a couple of lines of code. A test can simply be changed to check the contract has been deployed, if this passes - we are on our way to success! The rest of the test file (mostly commented) was lifted from other public projects to sanity check and can be scrapped (unless removed from the repo already at this point).

Github Repo: [https://github.com/BadAtBlockchain/harmony-truffle-yarn-base-project](https://github.com/BadAtBlockchain/harmony-truffle-yarn-base-project)

