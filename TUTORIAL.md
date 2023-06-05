# Greeter Envio indexer tutorial using Hardhat

This tutorial will take you through a step by step guide to building, deploying and indexing a Greeter smart contract using Envio and Hardhat. 

The final code for this tutorial can be seen [here](https://github.com/Float-Capital/envio-greeter-tutorial/)


<!-- Table of contents -->

- Dive into the terminal and make new project folder `mkdir envio-greeter-tutorial`
- Clone greeter [repo](https://github.com/PaulRBerg/hardhat-template) 
- Make a folder for your indexer - `mkdir envio-indexer`
- Open a second terminal so that you don't have to switch directories the whole time 
- npx envio init
- npx envio codegen
- pnpm start
- 

## Background

### [Greeter contract](https://github.com/Float-Capital/hardhat-template)

The Greeter contract is a very simple smart contract that allows user to write a greeting message on the blockchain.

### [Hardhat](https://hardhat.org/)

Hardhat is a development toolkit for smart contracts. In this tutorial we will be using it to deploy and interact with our smart contracts.

### [Envio](https://envio.dev)

Envio is a framework for developing a backend to index and aggregate blockchain data into a graphQL query-able database. 

## Pre-requisites

1. [<ins>Node.js</ins>](https://nodejs.org/en/download/current) we recommend using something like [fnm](https://github.com/Schniz/fnm) or [nvm](https://github.com/nvm-sh/nvm) to install Node
1. [<ins>pnpm</ins>](https://pnpm.io/installation)
1. [<ins>Docker Desktop</ins>](https://www.docker.com/products/docker-desktop/)

### Install Envio
```bash
npm i -g envio
```

## Step by step instructions

Here is an end to end list of unexplained commands

```bash
mkdir envio-greeter-tutorial
cd envio-greeter-tutorial
mkdir envio-indexer
cd envio-indexer
npx envio init
> Greeter
> Javascript
docker-compose down -v
docker-compose up -d
cd ..
git clone https://github.com/Float-Capital/hardhat-template.git
cd hardhat-template
rm -r -f deployments
pnpm i
cp .env.example .env
*Need to add ganache to docker compose thats copied across :/
pnpm hardhat deploy
pnpm hardhat task:setGreeting --account "1" --greeting "Hola"
pnpm hardhat task:setGreeting --account "2" --greeting "gm"
cd ../envio-indexer
npx envio codegen
pnpm start
./generated/register_tables_with_hasura.sh
open http://localhost:8080
```



## Start a Greeter project
`cd` into your project directory and run `envio init` command.
Then choose `Greeter` template and a language of choice to define the Event Handler.

This will auto-generate the setup files required for generating the indexing code.
More information on the setup files can be found in [Envio documentation](https://docs.envio.dev/docs/overview)

## Start a docker container
In order to run the indexer locally, you will need to spin up a docker container for:
- postGres server
- Ganache (local blockchain)

First open Docker Desktop.

You can first remove any stale data in docker by running:
```bash
docker-compose down -v
```

Then restart the docker by running:
```bash
docker-compose up -d
```

## Auto-generate indexing files
Now you are ready to generate the files required for indexing, by running:
```bash
envio codegen
```

This will auto-generate all the indexing files, as per specifications outline in the setup files.
All indexing files are written in Rescript, and they **do not** need to be modified to run the indexer.

## Deploy the smart contract onto ganache using HardHat
Copy *contracts* folder into your project folder.
`cd` into *contracts* and change the name of `.env.example` file to `.env`.

Follow instructions below:
```
cd contracts
rm -r -f deployments
pnpm hardhat deploy
```
Note that this will delete the previous deployment of the smart contract and re-deploy to prevent `node synced status` errors.

Check that the address of the deployment aligns with the address of smart contract in the `config.yaml` file, and if not, make sure they are the same.

More information on how to deploy contracts using Hardhat can be found [here](https://hardhat.org/hardhat-runner/docs/guides/deploying).

## Starting the indexer
Start the indexer by running:
```
pnpm start
```

## Running tasks
`task:setGreeting` will create a greeting for a user.

This task will require an account for the user [1-10] and a greeting string and can be called by running the following:
```bash
cd contracts
pnpm hardhat task:setGreeting --account "1" --greeting "Hola"
pnpm hardhat task:setGreeting --account "2" --greeting "gm"
```

`task:clearGreeting` will clear the latest greeting for a user.

This task only requires the account of user to be passed and can be called by running the following:
```bash
cd contracts
pnpm hardhat task:clearGreeting --account "#"
```

## Viewing the results

To view the data in the database, run
```bash
./generated/register_tables_with_hasura.sh
```

Navigate to http://localhost:8080/console to view local Hasura.

Admin-secret for Hasura is `testing` 

Alternatively you can open the file `index.html` for a cleaner experience (no Hasura stuff). Unfortunately, Hasura is currently not configured to make the data public.
