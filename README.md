
# Setting up a Blockchain Network

This document aims to provide instructions for setting up a blockchain network from scratch. All the techniques and assets used are developed by Ether-Authority team. This document should be treated confidential by the reader as is the doc's nature.




## Tools needed and Process

Full process:

- Create node package like this https://github.com/SecureChainAI/SCAI-Core
- Install 1st RPC & 1st Miner


## Creating node package
To create node package we need geth source and system-contracts source. Please understand that there is no single geth source that can be used in creation of any blockchain. Which geth source we'll use is based on project requirements. You can use this repo to get a basic structure of node package https://github.com/hide001/Testlab

### Creating genesis.json
Now get the appropriate system-contracts. Modify it accordingly and deploy Validators.sol, Punish.sol & Proposal.sol on any testnet. After deployment, copy the deployed bytecode and:

- open genesis.json file
- remove the bytecode from "code" field and paste the copied, deployed bytecode
example: 

"000000000000000000000000000000000000F000": {
      "balance":"0x0",
      "code": "0x6080604052600436......"
}


- paste Validators.sol's bytecode in 000000000000000000000000000000000000F000

- paste Punish.sol's bytecode in 000000000000000000000000000000000000F001

- paste Proposal.sol's bytecode in 000000000000000000000000000000000000F002

If epoch time and block period needs to be changed then also do it in the genesis.json


### Updating abi.go

- in the project directory, open file /node_src/consensus/congress/systemcontract/abi.go
- modify the ABI value which you can get by compiling Validators.sol, Punish.sol and Proposal.sol in remix IDE.

### Updating .env file
- modify the CHAINID variable


After all these modifications you can push the contents in the client's repo

# Creating 1st RPC & 1st Miner
- login to 1st miner server
```bash
      git clone <url>
```
```bash
      ./node-setup.sh --validator 1
```
- follow on-screen instructions until "All Task Passed" message is shown. DONT close the miner server window. DONT start the miner node
- copy the public address of the wallet created
- paste/replace this public address in the genesis.json on your local PC in extraData, alloc and coinbase fields
- do git push from your local PC 
- login to RPC server
- input these commands and enter in the given order 
```bash
      git clone <client's repo's git link>
```
```bash
      cd core-blockchain
```
```bash
      ./node-setup.sh --rpc
```
- follow the instructions and wait until "All Task Passed" message is shown 
```bash
      ./node-start.sh --rpc
```
```bash
      tmux attach -t node1
```
- you'll now get into node's interactive console, from there input the below command and enter
```bash
      admin.nodeInfo.enr
```
- copy the output and keep it safe, don't stop the RPC node
- update .env file in your local repo of the project by pasting bootnode value in the BOOTNODE variable of .env file
- once done, do git push to update the repo in GitHub
- now login/switch to the miner server
- make sure that node is not running here, verify it by entering tmux ls ...there should not be any running session
- remember now we are inside miner server, so follow below commands
```bash
      mkdir /root/wallet
```
```bash
      cp -r /root/core-blockchain/chaindata/node1/keystore /root/wallet
```
```bash
      cp /root/core-blockchain/chaindata/node1/pass.txt /root/wallet
```

```bash
      cd
      rm -rf /root/core-blockchain
```
```bash
      git clone <repo>
```
```bash
      cd /root/core-blockchain
```
```bash
      ./node-setup.sh --validator 1
```
- follow the on-screen instructions. Enter any password when you are asked to create one, it will not matter because we are not going to use this wallet. We'll be using the old wallet that we had copied to the /root/wallet/ directory above
- after "All task passed" message is shown, enter the below commands on MINER server
```bash
      rm -rf ./chaindata/node1/keystore
```
```bash
      rm -rf ./chaindata/node1/pass.txt
```
```bash
      cp -r /root/wallet/keystore ./chaindata/node1/keystore
```
```bash
      cp -r /root/wallet/pass.txt ./chaindata/node1/pass.txt
```
- we have now removed the new wallet and replaced it with the old wallet inside /root/wallet/ dir, this is also the same wallet that we used in genesis.json file
- now we can start the 1st validator/miner, to do that enter the below command
```bash
      ./node-start.sh --validator
```



