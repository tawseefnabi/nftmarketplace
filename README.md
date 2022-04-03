## Prerequisites
To be successful in this guide, you must have the following:

1. Node.js version `16.14.0` or greater installed on your machine. I recommend installing Node using either `nvm`.
2. `Metamask` wallet extension installed as a browser extension

The stack
In this guide, we will build out a full stack application using:

**Web application framework** - [Next.js](https://nextjs.org/)

**Solidity development environment** - [Hardhat](https://hardhat.org/)

**File Storage** - [IPFS](https://ipfs.io/)

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
Next, configure your template content paths in `tailwind.config.js`:
```js
/* tailwind.config.js */
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

```
Finally, delete the code in `styles/globals.css` and update it with the following:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```
**Configuring Hardhat**

Next, initialize a new Hardhat development environment from the root of your project:
```sh
npx hardhat

? What do you want to do? Create a basic sample project
? Hardhat project root: <Choose default path>
```
If you get an error referencing your **README.md**, delete README.md and run `npx hardhat` again.

Now you should see the following files and folders created for you in your root directory:

`hardhat.config.js `- The entirety of your Hardhat setup (i.e. your config, plugins, and custom tasks) is contained in this file.

`scripts` - A folder containing a script named sample-script.js that will deploy your smart contract when executed

`test` - A folder containing an example testing script

`contracts` - A folder holding an example Solidity smart contract

Next, update the configuration at `hardhat.config.js` with the following:
```js
/* hardhat.config.js */
require("@nomiclabs/hardhat-waffle")

module.exports = {
  defaultNetwork: "hardhat",
  networks: {
    hardhat: {
      chainId: 1337
    },
//  unused configuration commented out for now
//  mumbai: {
//    url: "https://rpc-mumbai.maticvigil.com",
//    accounts: [process.env.privateKey]
//  }
  },
  solidity: {
    version: "0.8.4",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
}
```

In this configuration, we've configured the local Hardhat development environment as well as the `Mumbai testnet`

Next, we'll create our smart contract!
Create a new file in the contracts directory named `NFTMarketplace.sol`.
functionality in contract are as:

- Updates the listing price of the contract
- Returns the listing price of the contract
- Mints a token and lists it in the marketplace
- allows someone to resell a token they have purchased
- Creates the sale of a marketplace item 
  - Transfers ownership of the item, as well as funds between parties
- Returns all unsold market items
- Returns only items that a user has purchased
- Returns only items a user has listed

**MarketItem**

The MarketItem struct allows us to store records of items that we want to make available in the marketplace.

**idToMarketItem**

This mapping allows us to create a key value pairing between IDs and MarketItems.

**createMarketItem**

This function transfers an NFT to the contract address of the market, and puts the item for sale.

**createMarketSale**

This function enables the transfer of the NFT as well as Eth between the buyer and seller.

**fetchMarketItems**

This function returns all market items that are still for sale.

**fetchMyNFTs**

This function returns the NFTs that the user has purchased.

Now the smart contract code and environment is complete and we can try testing it out.

To do so, we can create a local test to run through much of the functionality, like minting a token, putting it up for sale, selling it to a user, and querying for tokens.
## Running a local node and deploying the contract
Next, let's run a local node and deploy our contract to the node.

To do so, open your terminal and run the following command:

```sh
npx hardhat node
```

This should create and launch a local node.

Next, open another terminal and deploy the contracts:

```sh
npx hardhat run scripts/deploy.js --network localhost
```

When this completes successfully, you should have the addresses printed out to the console for your smart contract deployments.

Take those values and update the configuration in config.js:

```sh
export const nftmarketaddress = "market-contract-address"
export const nftaddress = "nft-contract-address"
```

## Running the app
Now, we should be able to run the app.

To do so, open the terminal and run the following command:

```sh
npm run dev
```

To create the test, open `test/sample-test.js` and update it with the following code:
```js
/* test/sample-test.js */
describe("NFTMarket", function() {
  it("Should create and execute market sales", async function() {
    /* deploy the marketplace */
    const NFTMarketplace = await ethers.getContractFactory("NFTMarketplace")
    const nftMarketplace = await NFTMarketplace.deploy()
    await nftMarketplace.deployed()

    let listingPrice = await nftMarketplace.getListingPrice()
    listingPrice = listingPrice.toString()

    const auctionPrice = ethers.utils.parseUnits('1', 'ether')

    /* create two tokens */
    await nftMarketplace.createToken("https://www.mytokenlocation.com", auctionPrice, { value: listingPrice })
    await nftMarketplace.createToken("https://www.mytokenlocation2.com", auctionPrice, { value: listingPrice })

    const [_, buyerAddress] = await ethers.getSigners()

    /* execute sale of token to another user */
    await nftMarketplace.connect(buyerAddress).createMarketSale(1, { value: auctionPrice })

    /* resell a token */
    await nftMarketplace.connect(buyerAddress).resellToken(1, auctionPrice, { value: listingPrice })

    /* query for and return the unsold items */
    items = await nftMarketplace.fetchMarketItems()
    items = await Promise.all(items.map(async i => {
      const tokenUri = await nftMarketplace.tokenURI(i.tokenId)
      let item = {
        price: i.price.toString(),
        tokenId: i.tokenId.toString(),
        seller: i.seller,
        owner: i.owner,
        tokenUri
      }
      return item
    }))
    console.log('items: ', items)
  })
})
```

Next, run the test from your command line:
```sh
npx hardhat test
```

If the test runs successfully, it should log out an array containing the two marketplace items.

![test-nft](https://res.cloudinary.com/practicaldev/image/fetch/s--KFwdsMsl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cewr5c4nkwudh8xd5etd.png)

## Building the front end
Now that the smart contract is working and ready to go, we can start building out the UI.

The first thing we might think about is setting up a layout so that we can enable some navigation that will persist across all pages.

To set this up, open `pages/_app.js` 

### Fetching NFTs

The first piece of functionality we'll implement will be fetching NFTs from the marketplace and rendering them to the UI.

To do so, open `pages/index.js` and update the loadNFTs function.
The loadNFTs function calls the `fetchMarketItems` function in our smart contract and returns all unsold NFTs.

We then map over each NFT to transform the response into something more readable for the user interface.

### Creating an NFT and placing it for sale

Next, let's create the function for minting the NFT and putting it for sale in the market.

To do so, open `pages/create-item.js` and update the `createSale` function 
This function writes two transactions to the network:

**createToken** mints the new token.

**createMarketItem** places the item for sale.

Once the item has been crated, we redict the user back to the main page.

### Allowing a user to purchase an NFT
Next, let's create the function for allowing a user to purchase an NFT.

To do so, open `pages/index.js` and update the `buyNft` function
This function writes the `createMarketSale` to the network, allowing the user to transfer the amount from their wallet to the seller's wallet.

### Allowing a user to view their own NFTs
The last piece of functionality we want to implement is to give users the ability to view the NFTs they have purchased. To do so, we'll be calling the `fetchMyNFTs` function from the smart contract.

Open pages/my-nfts.js and update the `loadNFTs` function

### Enabling listing fees
Next, we want the operator of the marketplace to collect listing fees. We can add this functionality with only a few lines of code.

First, open the `NFTMarket.sol` contract located in the contracts directory.

Here, we will set a listing price that you want to be using. We will also, create a variable that we can use to store the owner of the contract.

Add the following lines of code below the `_itemsSold` variable initialization:
```sh
address payable owner;
uint256 listingPrice = 0.01 ether;
```
Next, create a constructor to store the contract owner's address:

```sh
constructor() {
  owner = payable(msg.sender);
}
```
In the `createMarketItem` function, set a check to make sure that the listing fee has been passed into the transaction:

```sh
require(price > 0, "Price must be at least 1 wei"); // below this line 👇
require(msg.value == listingPrice, "Price must be equal to listing price");
```
Finally, in the `createMarketSale` function, send the `listingPrice` value to the contract owner once the sale is completed. This can go at the end of the function:

```sh
payable(owner).transfer(listingPrice);
```
Next, in the client-side code, open `pages/create-item.js` and add the payment value to be sent along with the transaction in the `createSale` function:

```sh
// above code omitted
const listingPrice = web3.utils.toWei('0.01', 'ether')

contract = new ethers.Contract(nftmarketaddress, Market.abi, signer)
transaction = await contract.createMarketItem(nftaddress, tokenId, price, { value: listingPrice })
// below code omitted
```