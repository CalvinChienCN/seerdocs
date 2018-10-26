# Guide to add SEER gateway access for exchanges and third-party platforms

The main purpose of this guide is to help exchanges and other third-party platforms (including but not limited to non-SEER DAPPs applications, centralized applications, etc.) to build a gateway to the SEER main network, list SEER main asset and tokens based on the SEER blockchain, such as PFC, OPC, etc.
Using SEER main network for charging and other operations, and the handling fee is far lower than SEER's ERC-20token and BTS asset, not affected by events such as Ethereum congestion.

## Fundamental

Gateway (deposits and withdrawals) used by most exchanges and third-party platforms is a centralized application service.
Taking the exchange as an example, the exchange provides a platform id for each user. Any user transfers money to the SEER main network account of the exchange and fills in the id in MEMO. When the SEER main network account of the exchange receives a transfer, the user can be credited according to the information filled in the MEMO, so that the user can be credited in the exchange platform.

## Ready to work

### Operating environment

The recommended server configuration is: 2vCPUs 4G memory 20G or more hard disk, Ubuntu 16.04.4 x64 system.

### Get account and private key

#### Register an account

Register the account through the SEER web wallet https://wallet.seer.best, where the account with the bar or number in the account is a common account name, which can be registered directly for free, such as `seer-exchange` or `seerdex01` and composed of pure English letters. The account name is the advanced user name, such as seerdex, needing to register through a lifetime membership account to pay the registration fee.

#### Get the private key

In the function of gateway, we need at least two pairs of keys of the account, namely the current active key and the current memo key. The active key allows you to transfer funds and other on-chain operation rights, and the memo key allows you to generate and read MEMO information associated with the account. <a href="https://docs.seerchain.org/#/zh-Hans/?id=%E6%9F%A5%E7%9C%8B%E6%82%A8%E7%9A%84%E7%A7%81%E9%92%A5">点击这里了解获取私钥的方法 </a> 。

## Configure a SEER full node

1、Create a new window called seer on the server；

```linux
screen -S seer
```

2、 Create a new directory named seer in the root directory, download the `v0.0.5 version` of the package to this directory, and rename it to `seer.tar.gz`. (Please go to the SEER Software Release page at https://github.com/seer-project/seer-core-package/releases and copy the latest ubuntu version package link to replace this download link.) 

 mkdir seer curl -Lo seer/seer.tar.gz https://github.com/seer-project/seer-core-package/releases/download/v0.05/seer-ubuntu-0.0.5.tar 

3. Enter the seer directory and extract the package.

 cd seer tar xzvf seer.tar.gz 
 
4. Start the witness_node with the websocket parameter:

 witness_node --rpc-endpoint=127.0.0.1:9090 partial-operations=true --track-account="\"seerdex-withdraw\"" --track-account="\"seerdex-deposit\"" max-ops-per-account=1000 
 
The --rpc-endpoint parameter is the websocket RPC IP address and port number of node monitor. You need to replace it. Here, 127.0.0.1 is the local machine, and 9090 is the WS port specified for the node.

For the node handling the deposit service, we do not need to save all the data, only need to save the account data related to the exchange account to save memory expenses, so we need to set the partial-operations parameter and the --track-account parameter, here Partial-operations=true means that only part of the data is needed. "\"seerdex-withdraw\"" and "\"seerdex-deposit\"" indicate one or more account ids to be tracked, which you need to replace.

The --max-ops-per-account parameter sets the number of operation records for the reserved account in memory. Here, 1000 indicates that 1000 operation records of the tracking account are retained, and you need to fill in as required.

5. After the observation node runs normally, ctrl+A d hides the screen and disconnects the server. After opening Sreeen with nodes, use screen -R or screen -r seer. After the node starts normally, it will display a blockout message of 3 seconds as shown below. If you want to close the node, use control + C.
