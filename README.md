# Taco Shop Scaffold

| Scaffold Property | Description             |
|-------------------|-------------------------|
| complexity        | beginner                |
| frontend dApp     | yes                     |
| automated tests   | yes                     |
| plugins           | LIGO, Taquito, Flextesa |

## Quickstart

Run the following commands to get up and running immediatley with this scaffold

Clone and initialize the scaffold:

- `taq scaffold https://github.com/ecadlabs/taqueria-scaffold-taco-shop taco-shop`
- `cd taco-shop`
- `taq init`
- `npm install`

Build and Start the dApp:

- `cd app`
- `npm install`
- `npm run build`
- `npm run start`

## Overview

This scaffold implements a simple full stack Tezos project. It has a simple React dApp that interacts with a smart contract which stores the number of available_tacos and provides a function to buy tacos

The React dApp uses Beacon Wallet to interact with Tezos wallets in the browser and once connected, will display the number of available_tacos stored on-chain in the smart contract. There is also a basic interface which allows the user to buy tacos by sending a transaction to the smart contract with the number_of_tacos_to_buy as a parameter

The smart contract has been deployed to ithacanet at this address for demonstration purposes: `KT1KBBk3PXkKmGZn3K6FkktqyPRpEbzJoEPE`

The project comes pre-configured with the following:

- Plugins: LIGO, Flextesa, Taquito 
- A LIGO smart contract `hello-tacos.mligo`  
- A network configuration for the Ithaca testnet
- A faucet file to fund operations on the testnet
- An environment named `ithacanet`

***Coming soon***

- Native Taqueria testing (Taqueria Jest plugin)
- Passing the deployed contract address to the React dA via the State API
- Deploying the contract using Taqueria operations
- Targeting a specific network for contract deployment and testing (sandboxes and testnets)
- Deploying the contract to mainnet

## Requirements

- Taqueria v0.3.1 or later
- Docker v0.9 or later
- Node.js v16 or later
- Beacon or Kukai Wallet
- A funded testnet account
- A [faucet file](https://teztnets.xyz)   

## Project Overview

## Project Structure

- `/.taq` - This hidden folder stores the project configuration and State
- `/app` - This folder contains the React dApp
- `/contracts` - This folder contains the LIGO smart contracts
- `/tests` - This folder contains the tests
- `/artifacts` - This folder contains the compiled Michelson `.tz` contracts

### Smart Contract

The smart contract `hello-tacos.mligo` is simple and straightforward. It stores the number of `available_tacos` in the contract storage, and provides an entrypoint that accepts a `tacos_to_buy` parameter which will decrease the number of available_tacos by the number of tacos_to_buy

```js
type available_tacos = nat
type tacos_to_buy = nat

let main (tacos_to_buy, available_tacos: tacos_to_buy * available_tacos): operation list * available_tacos =
    if tacos_to_buy > available_tacos
    then (failwith "NOT_ENOUGH_TACOS": operation list * available_tacos)
    else ([]: operation list), abs(available_tacos - tacos_to_buy)
```

### Testing

Coming soon with the Taqueria Jest plugin!

### React dApp

The React dApp retrieves the number of available tacos from the smart contract and displays the value. It provides an interface for the user to buy tacos and looks like this:

<img src={useBaseUrl('/img/hello-tacos-screenshot.png')} />
![Image of Taco Shop Frontend](/img/hello-tacos-screenshot.png)

> In order for the React dApp to connect to the smart contract, the contract must be deployed to the testnet and the returned address of the contract must be added to the `/dA/index.tsx` file. The scaffold comes pre-configured with the address of the deployed contract for demonstration purposes but it is recommended that you add your own faucet file, then re-deploy the contract and update the references to it in the project for your own use

> This will be fixed in the future when contract addresses will be passed via the State API dynamically
:::

## Requirements

- Node.js v16.x (or later)
- Docker v 0.8.0 (or later)
- Taqueria v0.3.1 (or later)
- Temple Wallet (latest version)

## Scaffold the Project

This project is available as a Taqueria scaffold. To create a new project from this scaffold, run the following command:

```shell
taq scaffold https://github.com/ecadlabs/taqueria-scaffold-taco-shop taco-shop
```

This will clone the Taco Shop scaffold project into the `taco-shop` directory

## Project Setup

To set up the project, first change into the project directory:

```shell
cd hello-tacos
```

Next, initialize the project with Taqueria:

```shell
taq init
```

Then install the dependencies:

```
npm install
```

## Using the Project

The intended workflow for this project is as follows:

1. Compile the LIGO source code
2. Originate the smart contract to the testnet
3. Insert the returned contract address into the React dA
4. Build and start the React dA
5. Connect to Temple wallet
6. Buy tacos!

### Compile the Contract

```shell
taq compile
```

This will compile the `/contracts/hello-tacos.mligo` contract to a file, `/artifacts/hello-tacos.tz`

### Originate to the Testnet

Run the following command to originate the contract to the ithacanet environment:

```shell
taq originate -e ithacanet
```

This should return the address of the contract on the testnet which looks like this:
```shell
┌────────────────┬──────────────────────────────────────┬────────────────┐
│ Contract       │ Address                              │ Destination    │
├────────────────┼──────────────────────────────────────┼────────────────┤
│ hello-tacos.tz │ KT1EHAXNQ1oeh2ZNTkvvcU6PfzQvGPLsgJx3 │ ithaca_network │
└────────────────┴──────────────────────────────────────┴────────────────┘
```

:::caution
This scaffold comes with a pre-configured faucet file for the testnet which is shared by all users and can cause issues. It is recommended that you replace the faucet file in the project's `config.json` file with your own which you can get from the [Teztnets Faucet](https://teztnets.xyz/). Further information about network configuration can be found [here](/src/docs/getting-started/networks)
:::


### Insert the Contract Address

Now that the contract has been deployed, you need to insert the address of the contract into the `/dA/index.tsx` file. Copy the address returned from the command above and paste it into the `contractAddress` variable in the `/dA/index.tsx` file as shown here:

```ts /dA/src/dA.tsx
function dA() {
  const [rpcUrl, setRpcUrl] = useState("https://ithacanet.ecadinfra.com");
  const [contractAddress, setContractAddress] = useState(
    "KT1EHAXNQ1oeh2ZNTkvvcU6PfzQvGPLsgJx3"
  );
  const [contractStorage, setContractStorage] = useState<number | undefined>(
    undefined
  );
  const [Tezos, setTezos] = useState<TezosToolkit>();
  const [connected, setConnected] = useState(false);
```

### Build and Start the React dA

Now that the contract has been deployed and the address added to the dA, you can build and start the React dA

Change into the `/dA` directory:

```shell
cd dA
```

Install dependencies:

```shell
npm install
```

Build the React dA:

```shell
npm run build
```

Start and serve the dA:

```shell 
npm run start
```

You should now be able to access the Taco shop dApp at `http://localhost:3000/`

### Connect to Temple Wallet

Open a browser and navigate to `http://localhost:3000/`

You should see the number of `available_tacos` displayed

Click on the `Connect wallet` button in the top right corner of the page and select `Temple Wallet`

Provide your credentials for the wallet, select a funded account, and click `connect`

### Buy Tacos

With your wallet connected, you can now interact with the contract entrypoint

Click the `order` button and then authorize the transaction in Temple Wallet

Once completed, you will see the value of `available_tacos` decrease in the dApp by the number of tacos you order
