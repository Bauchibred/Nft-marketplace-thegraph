
- [Full-Stack Setup](#full-stack-setup)
  - [1. Git clone the contracts repo](#1-git-clone-the-contracts-repo)
  - [2. Start your node](#2-start-your-node)
  - [3. Connect your codebase to your moralis server](#3-connect-your-codebase-to-your-moralis-server)
  - [4. Globally install the `moralis-admin-cli`](#4-globally-install-the-moralis-admin-cli)
  - [5. Setup your Moralis reverse proxy](#5-setup-your-moralis-reverse-proxy)
    - [IMPORTANT](#important)
  - [6. Setup your Cloud functions](#6-setup-your-cloud-functions)
  - [7. Add your event listeners](#7-add-your-event-listeners)
    - [You can do this programatically by running:](#you-can-do-this-programatically-by-running)
    - [Or, if you want to do it manually](#or-if-you-want-to-do-it-manually)
  - [8. Mint and List your NFT](#8-mint-and-list-your-nft)
  - [9. Start your front end](#9-start-your-front-end)
- [Minimal Quickstart](#minimal-quickstart)

# Full-Stack Setup

## 1. Git clone the contracts repo

In it's own terminal / command line, run: 

```
git clone https://github.com/Bauchibred/Nft-marketplace-thegraph
cd Nft-marketplace-thegraph
yarn
```

## 2. Start your node 

After installing dependencies, start a node on it's own terminal with:

```
yarn hardhat node
```

## 3. Connect your codebase to your moralis server

Setup your event [moralis](https://moralis.io/). You'll need a new moralis server to get started. 

Sign up for a [free account here](https://moralis.io/).

Once setup, update / create your `.env` file. You can use `.env.example` as a boilerplate. 

```
NEXT_PUBLIC_APP_ID=XXXX
NEXT_PUBLIC_SERVER_URL=XXXX
moralisApiKey=XXX
moralisSubdomain=XXX
masterKey=XXX
chainId=31337
```

With the values from your account. 

Then, in your `./package.json` update the following lines:
```
"moralis:sync": "moralis-admin-cli connect-local-devchain --chain hardhat --moralisSubdomain XXX.usemoralis.com --frpcPath ./frp/frpc",
"moralis:cloud": "moralis-admin-cli watch-cloud-folder  --moralisSubdomain XXX.usemoralis.com --autoSave 1 --moralisCloudfolder ./cloudFunctions",
"moralis:logs": "moralis-admin-cli get-logs --moralisSubdomain XXX.usemoralis.com"
```

Replace the `XXX.usemoralis.com` with your subdomain, like `4444acatycat.usemoralis.com` and update the `moralis:sync` script's path to your instance of `frp` (downloaded as part of the Moralis "Devchain Proxy Server" instructions mentioned above)

## 4. Globally install the `moralis-admin-cli`

```
yarn global add moralis-admin-cli
```

## 5. Setup your Moralis reverse proxy 

> Optionally: On your server, click on "View Details" and then "Devchain Proxy Server" and follow the instructions. You'll want to use the `hardhat` connection. 

- Download the latest reverse proxy code from [FRP](https://github.com/fatedier/frp/releases) and add the binary to `./frp/frpc`. 
- Replace your content in `frpc.ini`, based on your devchain. You can find the information on the `DevChain Proxy Server` tab of your moralis server. 

In some Windows Versions, FRP could be blocked by firewall, just use a older release, for example frp_0.34.3_windows_386

Mac / Windows Troubleshooting: https://docs.moralis.io/faq#frpc

Once you've got all this, you can run: 

```
yarn moralis:sync
``` 

You'll know you've done it right when you can see a green `connected` button after hitting the refresh symbol next to `DISCONNECTED`. *You'll want to keep this connection running*.

<img src="./img/connected.png" width="200" alt="Connected to Moralis Reverse Proxy">

### IMPORTANT

Anytime you reset your hardhat node, you'll need to press the `RESET LOCAL CHAIN` button on your UI!

## 6. Setup your Cloud functions

In a separate terminal (you'll have a few up throughout these steps)

Run `yarn moralis:cloud` in one terminal, and run `yarn moralis:logs` in another. If you don't have `moralis-admin-cli` installed already, install it globally with `yarn global add moralis-admin-cli`.

> Note: You can stop these after running them once if your server is at max CPU capactity. 

If you hit the little down arrow in your server, then hit `Cloud Functions` you should see text in there. 

<img src="./img/down-arrow.png" width="500" alt="Cloud Functions Up">
<img src="./img/functions.png" width="250" alt="Cloud Functions Up">

Make sure you've run `yarn moralis:sync` from the previous step to connect your local Hardhat devchain with your Moralis instance. You'll need these 3 moralis commands running at the same time. 

## 7. Add your event listeners

### You can do this programatically by running:

```
node watchEvents.js
```

### Or, if you want to do it manually

Finally, go to `View Details` -> `Sync` and hit `Add New Sync` -> `Sync and Watch Contract Events`

Add all 3 events by adding it's information, like so: 

1. ItemListed:
   1. Description: ItemListed
   2. Sync_historical: True
   3. Topic: ItemListed(address,address,uint256,uint256)
   4. Abi: 
```
{
  "anonymous": false,
  "inputs": [
    {
      "indexed": true,
      "internalType": "address",
      "name": "seller",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "address",
      "name": "nftAddress",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "uint256",
      "name": "tokenId",
      "type": "uint256"
    },
    {
      "indexed": false,
      "internalType": "uint256",
      "name": "price",
      "type": "uint256"
    }
  ],
  "name": "ItemListed",
  "type": "event"
}
```
    5. Address: <YOUR_NFT_MARKETPLACE_DEPLOYED_ADDRESS_FROM_HARDHAT>
    6. TableName: ItemListed

You can add the canceled and bought events later. 

## 8. Mint and List your NFT

Back in the main directory, run:

```
yarn hardhat run scripts/mint-and-list-item.js --network localhost
```

And you'll now have an NFT listed on your marketplace. 

## 9. Start your front end

At this point, you'll have a few terminals running:

- Your Hardhat Node
- Your Hardhat Node syncing with your moralis server
- Your Cloud Functions syncing
- Your Moralis Logging

And you're about to have one more for your front end. 

```
yarn run dev
```

And you'll have your front end, indexing service running, and blockchain running.

# Minimal Quickstart

1. Clone the backend repo

```
git clone https://github.com/PatrickAlphaC/hardhat-nft-marketplace-fcc
cd hardhat-nextjs-nft-marketplace-fcc
yarn
yarn hardhat node
```
Leave that terminal running^

2. Clone the frontend

```
git clone https://github.com/Bauchibred/Nft-marketplace-backend
cd nextjs-nft-marketplace-backend
yarn
```

Setup your `.env` with moralis info and update your `package.json` with moralis subdomain.

3. Sync your hardhat node with moralis

Update your `frpc.ini` file with what you see in the moralis UI.

Leave this terminal running: 

```
yarn moralis:sync
```

4. Watch for events && Update cloud functions

Run once:
```
yarn moralis:cloud
```

```
node watchEvents.js
```

5. Emit an event

Back in your hardhat project, run:

```
yarn hardhat run scripts/mint-and-list-item.js --network localhost
```

And you should see it updated in the database


Doing this so we can allow normal people to be able to interact with our code on our website

NEXTJS SETUP
yarn create next-app
remove the eslint

Then we go to pages remove everythign but the head tag

!. We want to make a home page
  -Home page shpws recently listed NFTs and if you own the NFTs we can update the listing if not we can buy
  2. Sell page
    -where we can sell our NFTS
    so we create our sell page

    Index page is our home page, so the first thing is our  connect button so our users can connect to our sit
    So we create our component folder and add a new file
    yarn add web3uikit moralis react-moralis, run in the command line not as dev dependencies.
    The moralis package is really important and even when we are not going to host on the moralis server all the hooks and tools from Moralis really helps

    so in app.js we 
    import {moralisprovider} from "react-moralis" 
    this is so we can use our web3uikit
    Then we wrap our whole component in a moralis provider
    We export our default function from headers where we just return our connect button
    AAnd then add it to our app.js just above our component
    In our headers now since we need a nav bar and other stuffs we return a lot of stuffs and then would have to put them in parentheses and to use links we import link, from next/Link
    Now we can create our links and then add them
    We do our formatting with tailwind on nextjs
    We can use the tailwind nextjs documentary to follow up
    we yarn add dev tailwind and then run tailwindcss init-p
    Then we can add our stylings for our nav and div in thr headers
  then lastly we remove the head from our index page snd add it to our header, that way no matter what page we are on we are always going to have the head information and there's no need to add it to every page.
    INDEXING IN WEB3
How do we get our most recent listings to show on our home page, we can't loop through the mapping of addresses cause that's a whole large number of data one can't go through and it can't be an array too cause that's going to be too gas expensive, so this is one of the reason why emitting events are very important and off chain services can access these events, so the best thing to do is actually create an offline server that's also going to keep an ear out to listen to these events and then we add them to a database, and this is not necessarily centralised using the graph oes it in a decentralised way but moralis those it in a centralised way but is much more faster, but to keep in mind that most part of our contracts is still in a decentralised setting in the sense that our smart contracts and all that and some centralised servers are realy important in the spac

MORALIS
Moralis also comes optionally with a server back in to help us with our development
Moralis is really helpful in the sense that it's a single workflow, and it's becoming one of the most adopted tech in the web3space

Connecting Moralis to our Local Hardhat Node so we create a server on moralis, after doing that we can then go on the moralis documentation to see the infos we need so for example we can search smart contract events to see what comes up

so now we want to use the moralis providers so instead of initialisingmount on false we change it we are going to give the moralisprovider our app id and server url as that way it get a direect acces to our server thst has been created on moralis, so we just get the info from our moralis database, grab our app id and the server url to add to our moralis provider and we should add it as environment variables
And in our env file we have to prefix our variables with NEXT_PUBLIC so that nextJs can know that this is a variable that's needed to be integrated in our nextAPP so then we stick them in to our moralis provider so now
We connect it to our contracts and then inform it on which events to listen out for and all that
So we now then run our backend code locally and then use the Devchain proxy server from moralis as how we connect moralis to our code that's been produced locally on hardhat, but first we have to download a reverse proxy
After downloading the reverse proxy, all we need is the frpc and frpc.ini so we can just import them to a folder in our project
So then we can go to the more opions on our moralis to get the hardhat cofig to use and add it to our frpc_ini file.
Then we run the command to set it uo in a different terminal, remember that i use a mac and we can run linux commands on mac so in the new terminkal we just cd into 
Then we run the linux command 
./frpc -c frpc.ini
There's another way to do this
This is using the moralis admin CLI
we run yarn global add moralis-admin-cli
and now if we run moralis-admin-cli
we'd have a lot of functions we can chose from
so we can just add a new command and script to our package.json file, so we can easily access it from our command line
so now we can then add our cli api to our env file so we don't need to add it ourselves to on the terminal, also we need to add the moralisapisecret

Moralis Event Sync
The first way to tell our moralis to listen for events is by using the UI and adding a sync, so while adding the new sync we chose the option where we watch contracts and listen to events
And then manually add everything, or wqe can do everything programmatically, 

Now we do everything programmatically but first we have to conect our moralis through sdk, i.e not just therough react 
so we use sdk and to do this we need to import moralis/node and our dotenv file check addEvents.js
And asides that we need to get the contract address our hardhat was deployed to, so to do that we are going to need to create a deploy script from our backend which is the Nft market place itself and then update to our front end.
We need to then read the fiile onto our front end code so for that we create our networkMapping.json under our constants file this file just keeps track of all of our deployment
then under our update front end from the backend we attach this json file that keeps track of our updates in relation to the backend file  then we also get the chain id  
so we update the contract address of the mapped file and then we need to write it back to our front end by using the line below
    fs.writeFileSync(frontEndContractsFile, JSON.stringify(contractAddresses))

    so we now have our network mapping file and then we add it in to our addEvents.js
    Then we create a new async function, but for this we need to get our server url and app id

    We are going to be using the Add new Event sync from code using moralis
        sync_historical: true, this allows us to get the history of all the events listed on our blockchain, 
        the topic is the name of the event and the type of our params
thrn the abi for just the event, so we just go to our nftmarketplace.json and get the abi for that particular event, so all we have to do is search the name of the event and then copy from where the name is at up until the anonymouse false
Then the tableName which is going to be the name of the table that gets updated on our moralis server so we do same for all events
So now we've indexed these events and whenever moralis hears anything about the listed events it attaches them to the server8
so now to send them up to our server we use
    const listedResponse = await Moralis.Cloud.run("watchContractEvent", itemListedOptions, {
        useMasterKey: true,
    })
for our item listing event and then do the same thing for the rest of the events
The moralis.cloud.com returns our response

Then we run node addEvents.js
Back in our backend there is a mint and list scripts, so we can list an Nft and see if our moralis is going to hear that

Some troubleshooting:
-Resetting the local chain
if we stop our hardhat node we need to reset the local chain on moralis so moralis knows that something has been done and to start listening for events again
Moralis Cloud Functions
This allows us to add anything we want our front end to do on our front end, so we can just add the long quote for calling moralis cloud to our package.json and from on there we can just run the short code to get our cloud updated  so we create an updateActiveitems.js

../nextjs-nft-marketplace-moralis-fcc/constants/

{"4":{"NftMarketplace":["0x97957cA0158c6def07FB5c6716f309FB3293303c"]},"31337":{"NftMarketplace":["0x5FbDB2315678afecb367f032d93F642f64180aa3"]}}
const frontEndContractsFile = "../NftmarketPlace-moralis/constants/networkMapping.json"


THE GRAPH INDEX
The code on here is similar to our moralis so we can just copy the moralis folder and delete some folders.
here we are going to run the deploy script for rinkeby form our backend, we can now deploy the basicNft since we've deployed the marketlplace and from there onwords, so we can manually add the address to which the nft was deployed to or it can get auto updated, we can also just delete the cloud functions folder since we are no longer going to use a server, we also do not need the frp folder
Also in our app.js we are going to initialize onamount false our moralis provider since we are no longer going to connect to it but rather just use the hooks, the main change we are going to have is for our index.js
Cause before hand we get our list of NFTs from the moralis query on our index.js
The graph is a decentralised layer for storing and indexing the data on the blockchain, using the graph, developers can create subgraphs and use it as an indexing layer to efficiently index data in a deterministic way, which is usually done via some http requests
Example of indexing in the real world is when search engines llike google crawl the internet indexing relevant data and making it avsilable for users to search with using their UI
To build a graph we visit thegraph.com and create a new subgraph using the UI, then we use thegraph CLI to scaffold out an empty sub graph boiler plate and update with our contract information
Thirdly we define the data model, network, contract addresses, and other configurations locally
Then we deploy the subgraph and then we can test it.
Right now we can't run our app cause it's indexing from the moralis server and not the graph
Building a Subgraph
We create a subgraph on the goerli network and then add 
But our nft-marketplace subgraph is going to need it's own reppository in which we build our commands locally
