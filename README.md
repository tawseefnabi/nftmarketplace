## Prerequisites
To be successful in this guide, you must have the following:

1. Node.js version `16.14.0` or greater installed on your machine. I recommend installing Node using either `nvm`.
2. `Metamask` wallet extension installed as a browser extension

The stack
In this guide, we will build out a full stack application using:

**Web application framework** - [Next.js](https://nextjs.org/)

**Solidity development environment** - [Hardhat](https://hardhat.org/)

**File Storage** - [IPFS]()
**Ethereum Web Client Library** - [Ethers.js](https://docs.ethers.io/v5/)

## About the project

The project that we will be building will be **Metaverse Marketplace** - an NFT marketplace.

When a user puts an NFT for sale, the ownership of the item will be transferred from the creator to the marketplace contract.

![Metaverse marketplace](https://res.cloudinary.com/practicaldev/image/fetch/s--1d8ljara--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lx8avrmuwqswx9roub2g.png)

When a user purchases an NFT, the purchase price will be transferred from the buyer to the seller and the item will be transferred from the marketplace to the buyer.

The marketplace owner will be able to set a listing fee. This fee will be taken from the seller and transferred to the contract owner upon completion of any sale, enabling the owner of the marketplace to earn recurring revenue from any sale transacted in the marketplace

The marketplace logic will consist of just one smart contract:

NFT Marketplace Contract - this contract allows users to mint NFTs and list them in a marketplace.

I believe this is a good project because the tools, techniques, and ideas we will be working with lay the foundation for many other types of applications on this stack – dealing with things like payments, commissions, and transfers of ownership on the contract level as well as how a client-side application would use this smart contract to build a performant and nice-looking user interface.,

In addition to the smart contract, I'll also show you how to build a subgraph to make the querying of data from the smart contract more flexible and efficient. As you will see, creating views on data sets and enabling various and performant data access patterns is hard to do directly from a smart contract. The Graph makes this much easier.

## Project setup
To get started, we'll create a new Next.js app. To do so, open your terminal. Create or change into a new empty directory and run the following command:
```sh
 npx create-next-app nft-marketplace
```
Next, change into the new directory and install the dependencies using a package manager like npm, yarn, or pnpm:
```sh
  cd nft-marketplace

  npm install ethers hardhat @nomiclabs/hardhat-waffle \
  ethereum-waffle chai @nomiclabs/hardhat-ethers \
  web3modal @openzeppelin/contracts ipfs-http-client \
  axios
```

Setting up Tailwind CSS
We'll be using [Tailwind CSS](https://tailwindcss.com/) for styling, we we will set that up in this step.

Tailwind is a utility-first CSS framework that makes it easy to add styling and create good looking websites without a lot of work.

Next, install the Tailwind dependencies:

```sh
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest

```

Next, we will create the configuration files needed for Tailwind to work with Next.js `tailwind.config.js` and `postcss.config.js` by running the following command:

```sh
npx tailwindcss init -p
```