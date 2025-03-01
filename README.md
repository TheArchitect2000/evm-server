<p align="center">
  <a href="https://fidesinnova.io/" target="blank"><img src="g-c-web-back.png" /></a>
</p>


# How to connect to the FidesInnova blockchain 

<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.com/invite/NQdM6JGwcs" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://twitter.com/FidesInnova" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>

To connect to the FidesInnova blockchain, you need to set up a node, configure it for network interaction, and use the appropriate tools or libraries for communication. You can set up your FidesInnova node in one of two ways:<br>
<p><b>General Node:</b> Connects to the blockchain, allowing you to interact with the network without participating in block validation.</p>
<p><b>Validator Node:</b> Actively participates in the consensus mechanism by validating transactions and securing the network.</p>


## 1- FidesInnova Block Producer Node - Validator
- Install Ethereum V1.13.x
- Copy the URL for your system from https://geth.ethereum.org/downloads
```
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.13.15-c5ba367e.tar.gz
tar -xvf geth-linux-amd64-1.13.15-c5ba367e.tar.gz
cd geth-linux-amd64-1.13.15-c5ba367e
chmod +x geth
sudo cp geth /usr/local/bin/
geth version
rm -rf geth-linux-amd64-1.13.15-c5ba367e.tar.gz
```
- Geth version 1.13 is for PoA consensus algorithm.
- Creating a new account
```
cd ~/
mkdir fides_blockchain
geth --datadir fides_blockchain account new
```
- The output should look like this:
`Public address of the key:   0xfBDfF421004493e13A4a69394c3b98f1e44C9874
Path of the secret key file: fides_blockchain/keystore/UTC--2024-02-27T16-17-52.880254508Z--fbdff421004493e13a4a69394c3b98f1e44c9874`
- Save the password
```
sudo nano fides_blockchain/password.sec
```
- Enter the account password and then save and exit the file.
- Create the genesis file
```
cd ~/
sudo nano genesis.json
```
- Copy and paste the file content from below and then edit the genesis
```
{
  "config": {
    "chainId": 706883,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "clique": {
      "period": 4,
      "epoch": 7500
    }
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "extradata": "0x000000000000000000000000000000000000000000000000000000000000000029807771835E7a8Ea638cD44BA8F417A683803270000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "alloc": {
    "29807771835E7a8Ea638cD44BA8F417A68380327": {
      "balance": "1000000000000000000000000000"
    }
  }
}
```
- Parameters:
  - _chainId_: 706883 is FidesInnova chain ID.<br>
  - _period_: Block time in seconds.<br>
  - _extradata_: To encode the signer addresses in extra data, concatenate 32 zero bytes, all signer addresses and 65 further zero bytes. The result of this concatenation is then used as the value accompanying the extradata key in genesis.json.<br>
  - _alloc_: Initial allocation of ether (alloc). This determines how much ether is available to the addresses listed in the genesis block. <br>
- Note: If you are a new Validator node, please contact FidesInnova team at info@fidesinnova.io to add your public address (i.e., wallet address) to the blockchain of the majority of the validator nodes in the network. Other validator nodes should use the following commands to add your node to their network. In the 'screen' that the validator node is running, type:
```
  clique.propose(<THE NEW NODE PUBLIC ADDRESS>, true)
```
- Start the Blockchain
```
geth --datadir fides_blockchain init genesis.json
```
- start syncing with the blockchain
```
geth --datadir "fides_blockchain" --port 3000 --ipcpath "fides_blockchain/geth.ipc" --networkid 706883 --http --http.port 8545 --http.addr 127.0.0.1 --http.corsdomain "*" --http.api "web3,eth,txpool,personal,net,network" --ws.api "eth,net,web3,network,txpool" --ws --ws.addr 0.0.0.0 --ws.port 8546 --ws.origins "*" --verbosity "0" console 2> "geth.log"
```
- In the node geth console, enter the enode address of the main node like: `admin.addPeer("enode/path@ip:port")`
```
admin.addPeer("enode://d23933dbde1faffbf6b6c16b6f3b143754803b5f03990551701f1d386bf87f0c133221802427ff4483eed77e9c33731aa9b83a7f579dab54a5e5cec773ccd812@65.108.196.41:3000")
```
- Verify Block Progress: Open the Geth console and use the following command:
```
eth.syncing
```
Note: When the node is not synchronized, it returns false, and when it is fully synchronized, it returns true.
- Check Syncing Status: To track the number of blocks your node has synced, run:
```
eth.blockNumber
```
Note: This command displays the current synchronized block number on your node, providing progress information about the synchronization process.
- Use this command to stop the blockchain node and then start the miner.
```
exit
```
- Start the Miner
Open a `screen` to run the node in background:<br>
<b>Note: Make sure to replace your **wallet address** in the command.</b>
```
geth --datadir "fides_blockchain" --port 3000 --ipcpath "fides_blockchain/geth.ipc" --networkid 706883  --unlock <INSERT_YOUR_WALLET_ADDRESS> --password "fides_blockchain/password.sec" --mine --miner.etherbase <INSERT_YOUR_WALLET_ADDRESS> console 2> "geth.log"
```
- Connect to Peer Nodes in FidesInnova Blockchain
In the node geth console, enter the enode address of the main node like: `admin.addPeer("enode/path@ip:port")`
```
admin.addPeer("enode://d23933dbde1faffbf6b6c16b6f3b143754803b5f03990551701f1d386bf87f0c133221802427ff4483eed77e9c33731aa9b83a7f579dab54a5e5cec773ccd812@65.108.196.41:3000")
```
<p></p>

## 2- FidesInnova General Node - RPC Provider
- Install Ethereum V1.13.x
- Copy the URL for your system from https://geth.ethereum.org/downloads
```
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.13.15-c5ba367e.tar.gz
tar -xvf geth-linux-amd64-1.13.15-c5ba367e.tar.gz
cd geth-linux-amd64-1.13.15-c5ba367e
chmod +x geth
sudo cp geth /usr/local/bin/
geth version
```

<!---
Version 1.14 is only for PoS
```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
sudo apt-get upgrade geth
```--->
- Create a new account
```
cd ~/
mkdir fides_blockchain
geth --datadir fides_blockchain account new
```
- The output should look like this:
`Public address of the key:   0xfBDfF421004493e13A4a69394c3b98f1e44C9874
Path of the secret key file: fides_blockchain/keystore/UTC--2024-02-27T16-17-52.880254508Z--fbdff421004493e13a4a69394c3b98f1e44c9874`

- Create the genesis file
```
cd ~/
sudo nano genesis.json
```
- Copy and paste the file content from below and then edit the genesis
```
{
  "config": {
    "chainId": 706883,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "clique": {
      "period": 4,
      "epoch": 7500
    }
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "extradata": "0x000000000000000000000000000000000000000000000000000000000000000029807771835E7a8Ea638cD44BA8F417A683803270000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "alloc": {
    "29807771835E7a8Ea638cD44BA8F417A68380327": {
      "balance": "1000000000000000000000000000"
    }
  }
}
```
_chainId_: 706883 is FidesInnova chain ID.<br>

_period_: Block time in seconds.<br>

_extradata_: To encode the signer addresses in extra data, concatenate 32 zero bytes, all signer addresses and 65 further zero bytes. The result of this concatenation is then used as the value accompanying the extradata key in genesis.json.<br>

_alloc_: Initial allocation of ether (alloc). This determines how much ether is available to the addresses listed in the genesis block. <br>

- Start the Blockchain
```
geth --datadir fides_blockchain init genesis.json
```

- Open a `screen` to run the node in background:
```
geth --datadir "fides_blockchain" --port 3000 --ipcpath "fides_blockchain/geth.ipc" --networkid 706883 --http --http.port 8545 --http.addr 127.0.0.1 --http.corsdomain "*" --http.api "web3,eth,txpool,personal,net,network" --ws.api "eth,net,web3,network,txpool" --ws --ws.addr 0.0.0.0 --ws.port 8546 --ws.origins "*" --verbosity "0" console 2> "geth.log"
```

- Connect to Peer Nodes in FidesInnova Blockchain
In the second node geth console, enter the enode address of the main node by this command : `admin.addPeer("enode/path@ip:port")`

```
admin.addPeer("enode://d23933dbde1faffbf6b6c16b6f3b143754803b5f03990551701f1d386bf87f0c133221802427ff4483eed77e9c33731aa9b83a7f579dab54a5e5cec773ccd812@65.108.196.41:3000")
```

## 3. Optional
- This section is only required if you want to create your own second node with global RPC access.
- Install nginx
```
sudo apt install nginx
```
- Open `nginx.conf` 
```
sudo nano /etc/nginx/nginx.conf
```
- Update the config file
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##
    
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;
    
	ssl_certificate  /etc/letsencrypt/live/<INSERT_YOUR_RPC_URL>/fullchain.pem;
	ssl_certificate_key  /etc/letsencrypt/live/<INSERT_YOUR_RPC_URL>/privkey.pem;


	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	#include /etc/nginx/conf.d/*.conf;
	#include /etc/nginx/sites-enabled/*;
    
	server {
		listen 443 ssl;
		listen [::]:443 ssl;
                server_name <INSERT_YOUR_RPC_URL>;

		location ^~ /ws {
			add_header Access-Control-Allow-Origin *;
        	add_header Access-Control-Allow-Headers *;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header Host $http_host;
			proxy_set_header X-NginX-Proxy true;
			proxy_pass http://127.0.0.1:8546/;
		}

		location ^~ / {
			add_header Access-Control-Allow-Origin *;
        	        add_header Access-Control-Allow-Headers *;
			proxy_pass http://127.0.0.1:8545/;
		}
	}
}
```

<!---
	server {
		listen 443 ssl;
		listen [::]:443 ssl;
                server_name <INSERT_YOUR_BLOCK_EXPLORER_URL>;

        location ^~ /{
			add_header Access-Control-Allow-Origin *;
        	        add_header Access-Control-Allow-Headers *;

			proxy_http_version    1.1;
			proxy_set_header      Host "$host";
			proxy_set_header      X-Real-IP "$remote_addr";
			proxy_set_header      X-Forwarded-For "$proxy_add_x_forwarded_for";
			proxy_set_header      X-Forwarded-Proto "$scheme";
			proxy_set_header      Upgrade "$http_upgrade";
			proxy_set_header      Connection connection_upgrade;
			proxy_cache_bypass    $http_upgrade;
			proxy_pass http://127.0.0.1/;
    	}

	}
--->
- Get an SSL vertififcate using certbot 
```
sudo apt-get update
sudo apt-get install certbot
sudo certbot certonly --standalone --preferred-challenges http
```
<b> Then enter your URL like: `fidesf1-rpc.fidesinnova.io` </b>
- Test nginx config
```
nginx -t
```
- Start nginx
```
systemctl restart nginx
```
- Useful Commands (optional)
```
apt update
apt install screen
screen -d
screen -r
admin.nodeInfo
clique.propose(address, true)
```


<!---
## 4- Blockscout (optional)
<b> Note: This section is only required if you want to use your own blockchain explorer.</b>

### 4-1 Basic Program Installation
```
sudo apt-get update && sudo apt-get upgrade
sudo apt install git 
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

### 4-2 docker install
```
sudo apt-get update
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
sudo apt-cache policy docker-ce
sudo apt install docker-ce
sudo apt-get install docker-compose-plugin
docker compose version
```
### 4-3 Install Node.js
```
sudo apt install nodejs
```
### 4-4 Download the BlockScout
```
cd ~/
sudo git clone https://github.com/blockscout/blockscout
cd blockscout/
sudo git checkout tags/v6.6.0-beta
```
### 4-5 Update BlockScout files
**`/root/blockscout/docker-compose/docker-compose.yml`**
```
version: '3.9'

services:
  redis-db:
    extends:
      file: ./services/redis.yml
      service: redis-db

  db-init:
    extends:
      file: ./services/db.yml
      service: db-init

  db:
    depends_on:
      db-init:
        condition: service_completed_successfully
    extends:
      file: ./services/db.yml
      service: db

  backend:
    depends_on:
      - db
      - redis-db
    extends:
      file: ./services/backend.yml
      service: backend
    build:
      context: ..
      dockerfile: ./docker/Dockerfile
      args:
        CACHE_EXCHANGE_RATES_PERIOD: ""
        API_V1_READ_METHODS_DISABLED: "false"
        DISABLE_WEBAPP: "false"
        API_V1_WRITE_METHODS_DISABLED: "false"
        CACHE_TOTAL_GAS_USAGE_COUNTER_ENABLED: ""
        CACHE_ADDRESS_WITH_BALANCES_UPDATE_INTERVAL: ""
        ADMIN_PANEL_ENABLED: ""
        RELEASE_VERSION: 6.6.0
    links:
      - db:database
    environment:
        ETHEREUM_JSONRPC_HTTP_URL: https://fidesf1-rpc.fidesinnova.io:443/
        ETHEREUM_JSONRPC_TRACE_URL: https://fidesf1-rpc.fidesinnova.io:443/
        ETHEREUM_JSONRPC_WS_URL: wss://fidesf1-rpc.fidesinnova.io:443/ws/
        CHAIN_ID: '706883'

  visualizer:
    extends:
      file: ./services/visualizer.yml
      service: visualizer

  sig-provider:
    extends:
      file: ./services/sig-provider.yml
      service: sig-provider

  frontend:
    depends_on:
      - backend
    extends:
      file: ./services/frontend.yml
      service: frontend

  stats-db-init:
    extends:
      file: ./services/stats.yml
      service: stats-db-init

  stats-db:
    depends_on:
      stats-db-init:
        condition: service_completed_successfully
    extends:
      file: ./services/stats.yml
      service: stats-db

  stats:
    depends_on:
      - stats-db
      - backend
    extends:
      file: ./services/stats.yml
      service: stats

  user-ops-indexer:
    depends_on:
      - db
      - backend
    extends:
      file: ./services/user-ops-indexer.yml
      service: user-ops-indexer

  proxy:
    depends_on:
      - backend
      - frontend
      - stats
    extends:
      file: ./services/nginx.yml
      service: proxy

```


**`/root/blockscout/docker-compose/envs/common-blockscout.env`**
```
ETHEREUM_JSONRPC_VARIANT=geth
ETHEREUM_JSONRPC_HTTP_URL=https://fidesf1-rpc.fidesinnova.io:443/
# ETHEREUM_JSONRPC_FALLBACK_HTTP_URL=
DATABASE_URL=postgresql://blockscout:ceWb1MeLBEeOIfk65gU8EjF8@db:5432/blockscout
# DATABASE_QUEUE_TARGET
# TEST_DATABASE_URL=
# TEST_DATABASE_READ_ONLY_API_URL=
ETHEREUM_JSONRPC_TRACE_URL=https://fidesf1-rpc.fidesinnova.io:443/
# ETHEREUM_JSONRPC_FALLBACK_TRACE_URL=
# ETHEREUM_JSONRPC_FALLBACK_ETH_CALL_URL=
# ETHEREUM_JSONRPC_ETH_CALL_URL=
# ETHEREUM_JSONRPC_HTTP_TIMEOUT=
# CHAIN_TYPE=
NETWORK=TESTNET
SUBNETWORK=FidesInnova
LOGO=/images/blockscout_logo.svg
ETHEREUM_JSONRPC_WS_URL=wss://fidesf1-rpc.fidesinnova.io:443/ws
ETHEREUM_JSONRPC_TRANSPORT=http
ETHEREUM_JSONRPC_DISABLE_ARCHIVE_BALANCES=false
# ETHEREUM_JSONRPC_ARCHIVE_BALANCES_WINDOW=200
# ETHEREUM_JSONRPC_HTTP_HEADERS=
# ETHEREUM_JSONRPC_WAIT_PER_TIMEOUT=
# ETHEREUM_JSONRPC_GETH_TRACE_BY_BLOCK=
IPC_PATH=
NETWORK_PATH=/
BLOCKSCOUT_HOST=
BLOCKSCOUT_PROTOCOL=
SECRET_KEY_BASE=56NtB48ear7+wMSf0IQuWDAAazhpb31qyc7GiyspBP2vh7t5zlCsF5QDv76chXeN
# CHECK_ORIGIN=
PORT=4000
COIN_NAME=FDS
# METADATA_CONTRACT=
# VALIDATORS_CONTRACT=
# KEYS_MANAGER_CONTRACT=
# REWARDS_CONTRACT=
# TOKEN_BRIDGE_CONTRACT=
EMISSION_FORMAT=DEFAULT
# CHAIN_SPEC_PATH=
# SUPPLY_MODULE=
COIN=FDS
EXCHANGE_RATES_COIN=
# EXCHANGE_RATES_MARKET_CAP_SOURCE=
# EXCHANGE_RATES_TVL_SOURCE=
# EXCHANGE_RATES_PRICE_SOURCE=
# EXCHANGE_RATES_COINGECKO_COIN_ID=
# EXCHANGE_RATES_COINGECKO_SECONDARY_COIN_ID=
# EXCHANGE_RATES_COINGECKO_API_KEY=
# EXCHANGE_RATES_COINGECKO_BASE_URL=
# EXCHANGE_RATES_COINGECKO_BASE_PRO_URL=
# EXCHANGE_RATES_COINMARKETCAP_BASE_URL=
# EXCHANGE_RATES_COINMARKETCAP_API_KEY=
# EXCHANGE_RATES_COINMARKETCAP_COIN_ID=
# EXCHANGE_RATES_COINMARKETCAP_SECONDARY_COIN_ID=
# EXCHANGE_RATES_CRYPTOCOMPARE_SECONDARY_COIN_SYMBOL=
POOL_SIZE=80
# EXCHANGE_RATES_COINGECKO_PLATFORM_ID=
# TOKEN_EXCHANGE_RATE_INTERVAL=
# TOKEN_EXCHANGE_RATE_REFETCH_INTERVAL=
# TOKEN_EXCHANGE_RATE_MAX_BATCH_SIZE=
# DISABLE_TOKEN_EXCHANGE_RATE=
POOL_SIZE_API=10
ECTO_USE_SSL=false
# DATADOG_HOST=
# DATADOG_PORT=
# SPANDEX_BATCH_SIZE=
# SPANDEX_SYNC_THRESHOLD=
HEART_BEAT_TIMEOUT=30
# HEART_COMMAND=
# BLOCKSCOUT_VERSION=
RELEASE_LINK=
BLOCK_TRANSFORMER=base
# BLOCK_RANGES=
# FIRST_BLOCK=
# LAST_BLOCK=
# TRACE_BLOCK_RANGES=
# TRACE_FIRST_BLOCK=
# TRACE_LAST_BLOCK=
# FOOTER_CHAT_LINK=
# FOOTER_FORUM_LINK_ENABLED=
# FOOTER_FORUM_LINK=
# FOOTER_TELEGRAM_LINK_ENABLED=
# FOOTER_TELEGRAM_LINK=
# FOOTER_GITHUB_LINK=
FOOTER_LOGO=/images/blockscout_logo.svg
FOOTER_LINK_TO_OTHER_EXPLORERS=false
FOOTER_OTHER_EXPLORERS={}
SUPPORTED_CHAINS={}
CACHE_BLOCK_COUNT_PERIOD=7200
CACHE_TXS_COUNT_PERIOD=7200
CACHE_ADDRESS_SUM_PERIOD=3600
CACHE_TOTAL_GAS_USAGE_PERIOD=3600
CACHE_ADDRESS_TRANSACTIONS_GAS_USAGE_COUNTER_PERIOD=1800
CACHE_TOKEN_HOLDERS_COUNTER_PERIOD=3600
CACHE_TOKEN_TRANSFERS_COUNTER_PERIOD=3600
CACHE_ADDRESS_WITH_BALANCES_UPDATE_INTERVAL=1800
CACHE_AVERAGE_BLOCK_PERIOD=1800
CACHE_MARKET_HISTORY_PERIOD=21600
CACHE_ADDRESS_TRANSACTIONS_COUNTER_PERIOD=1800
CACHE_ADDRESS_TOKENS_USD_SUM_PERIOD=3600
CACHE_ADDRESS_TOKEN_TRANSFERS_COUNTER_PERIOD=1800
# CACHE_TRANSACTIONS_24H_STATS_PERIOD=
# CACHE_FRESH_PENDING_TRANSACTIONS_COUNTER_PERIOD=
# TOKEN_BALANCE_ON_DEMAND_FETCHER_THRESHOLD=
# COIN_BALANCE_ON_DEMAND_FETCHER_THRESHOLD=
# CONTRACT_CODE_ON_DEMAND_FETCHER_THRESHOLD=
TOKEN_METADATA_UPDATE_INTERVAL=172800
CONTRACT_VERIFICATION_ALLOWED_SOLIDITY_EVM_VERSIONS=homestead,tangerineWhistle,spuriousDragon,byzantium,constantinople,petersburg,istanbul,berlin,london,paris,shanghai,cancun,default
CONTRACT_VERIFICATION_ALLOWED_VYPER_EVM_VERSIONS=byzantium,constantinople,petersburg,istanbul,berlin,paris,shanghai,cancun,default
# CONTRACT_VERIFICATION_MAX_LIBRARIES=10
CONTRACT_MAX_STRING_LENGTH_WITHOUT_TRIMMING=2040
# CONTRACT_DISABLE_INTERACTION=
# CONTRACT_AUDIT_REPORTS_AIRTABLE_URL=
# CONTRACT_AUDIT_REPORTS_AIRTABLE_API_KEY=
# CONTRACT_CERTIFIED_LIST=
UNCLES_IN_AVERAGE_BLOCK_TIME=false
DISABLE_WEBAPP=false
API_V2_ENABLED=true
API_V1_READ_METHODS_DISABLED=false
API_V1_WRITE_METHODS_DISABLED=false
# API_RATE_LIMIT_DISABLED=true
# API_SENSITIVE_ENDPOINTS_KEY=
API_RATE_LIMIT_TIME_INTERVAL=1s
API_RATE_LIMIT_BY_IP_TIME_INTERVAL=5m
API_RATE_LIMIT=50
API_RATE_LIMIT_BY_KEY=50
API_RATE_LIMIT_BY_WHITELISTED_IP=50
API_RATE_LIMIT_WHITELISTED_IPS=
API_RATE_LIMIT_STATIC_API_KEY=
API_RATE_LIMIT_UI_V2_WITH_TOKEN=5
API_RATE_LIMIT_BY_IP=3000
# API_GRAPHQL_ENABLED=
# API_GRAPHQL_MAX_COMPLEXITY=
# API_GRAPHQL_TOKEN_LIMIT=
# API_GRAPHQL_DEFAULT_TRANSACTION_HASH=
# API_GRAPHQL_RATE_LIMIT_DISABLED=
# API_GRAPHQL_RATE_LIMIT=
# API_GRAPHQL_RATE_LIMIT_BY_KEY=
# API_GRAPHQL_RATE_LIMIT_TIME_INTERVAL=
# API_GRAPHQL_RATE_LIMIT_BY_IP=
# API_GRAPHQL_RATE_LIMIT_BY_IP_TIME_INTERVAL=
# API_GRAPHQL_RATE_LIMIT_STATIC_API_KEY=
DISABLE_INDEXER=false
DISABLE_REALTIME_INDEXER=false
DISABLE_CATCHUP_INDEXER=false
INDEXER_DISABLE_ADDRESS_COIN_BALANCE_FETCHER=false
INDEXER_DISABLE_TOKEN_INSTANCE_REALTIME_FETCHER=false
INDEXER_DISABLE_TOKEN_INSTANCE_RETRY_FETCHER=false
INDEXER_DISABLE_TOKEN_INSTANCE_SANITIZE_FETCHER=false
INDEXER_DISABLE_TOKEN_INSTANCE_LEGACY_SANITIZE_FETCHER=false
INDEXER_DISABLE_PENDING_TRANSACTIONS_FETCHER=false
INDEXER_DISABLE_INTERNAL_TRANSACTIONS_FETCHER=false
# INDEXER_DISABLE_CATALOGED_TOKEN_UPDATER_FETCHER=
# INDEXER_DISABLE_BLOCK_REWARD_FETCHER=
# INDEXER_DISABLE_EMPTY_BLOCKS_SANITIZER=
# INDEXER_DISABLE_WITHDRAWALS_FETCHER=
# INDEXER_CATCHUP_BLOCKS_BATCH_SIZE=
# INDEXER_CATCHUP_BLOCKS_CONCURRENCY=
# INDEXER_CATCHUP_BLOCK_INTERVAL=
# INDEXER_EMPTY_BLOCKS_SANITIZER_INTERVAL=
# INDEXER_INTERNAL_TRANSACTIONS_BATCH_SIZE=
# INDEXER_INTERNAL_TRANSACTIONS_CONCURRENCY=
# ETHEREUM_JSONRPC_DEBUG_TRACE_TRANSACTION_TIMEOUT=
# INDEXER_BLOCK_REWARD_BATCH_SIZE=
# INDEXER_BLOCK_REWARD_CONCURRENCY=
# INDEXER_TOKEN_INSTANCE_USE_BASE_URI_RETRY=
# INDEXER_TOKEN_INSTANCE_RETRY_REFETCH_INTERVAL=
# INDEXER_TOKEN_INSTANCE_RETRY_BATCH_SIZE=10
# INDEXER_TOKEN_INSTANCE_RETRY_CONCURRENCY=
# INDEXER_TOKEN_INSTANCE_REALTIME_BATCH_SIZE=1
# INDEXER_TOKEN_INSTANCE_REALTIME_CONCURRENCY=
# INDEXER_TOKEN_INSTANCE_SANITIZE_BATCH_SIZE=10
# INDEXER_TOKEN_INSTANCE_SANITIZE_CONCURRENCY=
# INDEXER_TOKEN_INSTANCE_LEGACY_SANITIZE_BATCH_SIZE=10
# INDEXER_TOKEN_INSTANCE_LEGACY_SANITIZE_CONCURRENCY=10
# INDEXER_DISABLE_TOKEN_INSTANCE_ERC_1155_SANITIZE_FETCHER=false
# INDEXER_DISABLE_TOKEN_INSTANCE_ERC_721_SANITIZE_FETCHER=false
# INDEXER_TOKEN_INSTANCE_ERC_1155_SANITIZE_CONCURRENCY=2
# INDEXER_TOKEN_INSTANCE_ERC_1155_SANITIZE_BATCH_SIZE=10
# INDEXER_TOKEN_INSTANCE_ERC_721_SANITIZE_CONCURRENCY=2
# INDEXER_TOKEN_INSTANCE_ERC_721_SANITIZE_BATCH_SIZE=10
# INDEXER_TOKEN_INSTANCE_ERC_721_SANITIZE_TOKENS_BATCH_SIZE=100
# TOKEN_INSTANCE_OWNER_MIGRATION_CONCURRENCY=5
# TOKEN_INSTANCE_OWNER_MIGRATION_BATCH_SIZE=50
# INDEXER_COIN_BALANCES_BATCH_SIZE=
# INDEXER_COIN_BALANCES_CONCURRENCY=
# INDEXER_RECEIPTS_BATCH_SIZE=
# INDEXER_RECEIPTS_CONCURRENCY=
# INDEXER_TOKEN_CONCURRENCY=
# INDEXER_TOKEN_BALANCES_BATCH_SIZE=
# INDEXER_TOKEN_BALANCES_CONCURRENCY=
# INDEXER_TX_ACTIONS_ENABLE=
# INDEXER_TX_ACTIONS_MAX_TOKEN_CACHE_SIZE=
# INDEXER_TX_ACTIONS_REINDEX_FIRST_BLOCK=
# INDEXER_TX_ACTIONS_REINDEX_LAST_BLOCK=
# INDEXER_TX_ACTIONS_REINDEX_PROTOCOLS=
# INDEXER_TX_ACTIONS_AAVE_V3_POOL_CONTRACT=
# INDEXER_POLYGON_EDGE_L1_RPC=
# INDEXER_POLYGON_EDGE_L1_EXIT_HELPER_CONTRACT=
# INDEXER_POLYGON_EDGE_L1_WITHDRAWALS_START_BLOCK=
# INDEXER_POLYGON_EDGE_L1_STATE_SENDER_CONTRACT=
# INDEXER_POLYGON_EDGE_L1_DEPOSITS_START_BLOCK=
# INDEXER_POLYGON_EDGE_L2_STATE_SENDER_CONTRACT=
# INDEXER_POLYGON_EDGE_L2_WITHDRAWALS_START_BLOCK=
# INDEXER_POLYGON_EDGE_L2_STATE_RECEIVER_CONTRACT=
# INDEXER_POLYGON_EDGE_L2_DEPOSITS_START_BLOCK=
# INDEXER_POLYGON_EDGE_ETH_GET_LOGS_RANGE_SIZE=
# INDEXER_POLYGON_ZKEVM_BATCHES_ENABLED=
# INDEXER_POLYGON_ZKEVM_BATCHES_CHUNK_SIZE=
# INDEXER_POLYGON_ZKEVM_BATCHES_RECHECK_INTERVAL=
# INDEXER_POLYGON_ZKEVM_L1_RPC=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_START_BLOCK=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_CONTRACT=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_NATIVE_SYMBOL=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_NATIVE_DECIMALS=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_NETWORK_ID=
# INDEXER_POLYGON_ZKEVM_L1_BRIDGE_ROLLUP_INDEX=
# INDEXER_POLYGON_ZKEVM_L2_BRIDGE_START_BLOCK=
# INDEXER_POLYGON_ZKEVM_L2_BRIDGE_CONTRACT=
# INDEXER_POLYGON_ZKEVM_L2_BRIDGE_NETWORK_ID=
# INDEXER_POLYGON_ZKEVM_L2_BRIDGE_ROLLUP_INDEX=
# INDEXER_ZKSYNC_BATCHES_ENABLED=
# INDEXER_ZKSYNC_BATCHES_CHUNK_SIZE=
# INDEXER_ZKSYNC_NEW_BATCHES_MAX_RANGE=
# INDEXER_ZKSYNC_NEW_BATCHES_RECHECK_INTERVAL=
# INDEXER_ZKSYNC_L1_RPC=
# INDEXER_ZKSYNC_BATCHES_STATUS_RECHECK_INTERVAL=
# INDEXER_REALTIME_FETCHER_MAX_GAP=
# INDEXER_FETCHER_INIT_QUERY_LIMIT=
# INDEXER_TOKEN_BALANCES_FETCHER_INIT_QUERY_LIMIT=
# INDEXER_COIN_BALANCES_FETCHER_INIT_QUERY_LIMIT=
# INDEXER_GRACEFUL_SHUTDOWN_PERIOD=
# WITHDRAWALS_FIRST_BLOCK=
# INDEXER_OPTIMISM_L1_RPC=
# INDEXER_OPTIMISM_L1_BATCH_START_BLOCK=
# INDEXER_OPTIMISM_L1_BATCH_INBOX=
# INDEXER_OPTIMISM_L1_BATCH_SUBMITTER=
# INDEXER_OPTIMISM_L1_BATCH_BLOCKS_CHUNK_SIZE=
# INDEXER_OPTIMISM_L2_BATCH_GENESIS_BLOCK_NUMBER=
# INDEXER_OPTIMISM_L1_PORTAL_CONTRACT=
# INDEXER_OPTIMISM_L1_OUTPUT_ROOTS_START_BLOCK=
# INDEXER_OPTIMISM_L1_OUTPUT_ORACLE_CONTRACT=
# INDEXER_OPTIMISM_L1_WITHDRAWALS_START_BLOCK=
# INDEXER_OPTIMISM_L2_WITHDRAWALS_START_BLOCK=
# INDEXER_OPTIMISM_L2_MESSAGE_PASSER_CONTRACT=
# INDEXER_OPTIMISM_L1_DEPOSITS_START_BLOCK=
# INDEXER_OPTIMISM_L1_DEPOSITS_BATCH_SIZE=
# ROOTSTOCK_REMASC_ADDRESS=
# ROOTSTOCK_BRIDGE_ADDRESS=
# ROOTSTOCK_LOCKED_BTC_CACHE_PERIOD=
# ROOTSTOCK_LOCKING_CAP=
# INDEXER_DISABLE_ROOTSTOCK_DATA_FETCHER=
# INDEXER_ROOTSTOCK_DATA_FETCHER_INTERVAL=
# INDEXER_ROOTSTOCK_DATA_FETCHER_BATCH_SIZE=
# INDEXER_ROOTSTOCK_DATA_FETCHER_CONCURRENCY=
# INDEXER_ROOTSTOCK_DATA_FETCHER_DB_BATCH_SIZE=
# INDEXER_BEACON_RPC_URL=http://localhost:5052
# INDEXER_DISABLE_BEACON_BLOB_FETCHER=
# INDEXER_BEACON_BLOB_FETCHER_SLOT_DURATION=12
# INDEXER_BEACON_BLOB_FETCHER_REFERENCE_SLOT=8000000
# INDEXER_BEACON_BLOB_FETCHER_REFERENCE_TIMESTAMP=1702824023
# INDEXER_BEACON_BLOB_FETCHER_START_BLOCK=19200000
# INDEXER_BEACON_BLOB_FETCHER_END_BLOCK=0
# TOKEN_ID_MIGRATION_FIRST_BLOCK=
# TOKEN_ID_MIGRATION_CONCURRENCY=
# TOKEN_ID_MIGRATION_BATCH_SIZE=
# INDEXER_INTERNAL_TRANSACTIONS_TRACER_TYPE=
# WEBAPP_URL=
# API_URL=
SHOW_ADDRESS_MARKETCAP_PERCENTAGE=true
CHECKSUM_ADDRESS_HASHES=true
CHECKSUM_FUNCTION=eth
DISABLE_EXCHANGE_RATES=true
TXS_STATS_ENABLED=true
SHOW_PRICE_CHART=false
SHOW_PRICE_CHART_LEGEND=false
SHOW_TXS_CHART=true
TXS_HISTORIAN_INIT_LAG=0
TXS_STATS_DAYS_TO_COMPILE_AT_INIT=10
COIN_BALANCE_HISTORY_DAYS=90
APPS_MENU=true
EXTERNAL_APPS=[]
# GAS_PRICE=
# GAS_PRICE_ORACLE_CACHE_PERIOD=
# GAS_PRICE_ORACLE_SIMPLE_TRANSACTION_GAS=
# GAS_PRICE_ORACLE_NUM_OF_BLOCKS=
# GAS_PRICE_ORACLE_SAFELOW_PERCENTILE=
# GAS_PRICE_ORACLE_AVERAGE_PERCENTILE=
# GAS_PRICE_ORACLE_FAST_PERCENTILE=
# GAS_PRICE_ORACLE_SAFELOW_TIME_COEFFICIENT=
# GAS_PRICE_ORACLE_AVERAGE_TIME_COEFFICIENT=
# GAS_PRICE_ORACLE_FAST_TIME_COEFFICIENT=
# RESTRICTED_LIST=
# RESTRICTED_LIST_KEY=
SHOW_MAINTENANCE_ALERT=false
MAINTENANCE_ALERT_MESSAGE=
CHAIN_ID=706883
MAX_SIZE_UNLESS_HIDE_ARRAY=50
HIDE_BLOCK_MINER=false
DISPLAY_TOKEN_ICONS=false
RE_CAPTCHA_SECRET_KEY=
RE_CAPTCHA_CLIENT_KEY=
RE_CAPTCHA_V3_SECRET_KEY=
RE_CAPTCHA_V3_CLIENT_KEY=
RE_CAPTCHA_DISABLED=false
JSON_RPC=
# API_RATE_LIMIT_HAMMER_REDIS_URL=redis://redis-db:6379/1
# API_RATE_LIMIT_IS_BLOCKSCOUT_BEHIND_PROXY=false
API_RATE_LIMIT_UI_V2_TOKEN_TTL_IN_SECONDS=18000
FETCH_REWARDS_WAY=trace_block
MICROSERVICE_SC_VERIFIER_ENABLED=true
# MICROSERVICE_SC_VERIFIER_URL=http://smart-contract-verifier:8050/
# MICROSERVICE_SC_VERIFIER_TYPE=sc_verifier
MICROSERVICE_SC_VERIFIER_URL=https://eth-bytecode-db.services.blockscout.com/
MICROSERVICE_SC_VERIFIER_TYPE=eth_bytecode_db
MICROSERVICE_ETH_BYTECODE_DB_INTERVAL_BETWEEN_LOOKUPS=10m
MICROSERVICE_ETH_BYTECODE_DB_MAX_LOOKUPS_CONCURRENCY=10
MICROSERVICE_VISUALIZE_SOL2UML_ENABLED=true
MICROSERVICE_VISUALIZE_SOL2UML_URL=http://visualizer:8050/
MICROSERVICE_SIG_PROVIDER_ENABLED=true
MICROSERVICE_SIG_PROVIDER_URL=http://sig-provider:8050/
# MICROSERVICE_BENS_URL=
# MICROSERVICE_BENS_ENABLED=
MICROSERVICE_ACCOUNT_ABSTRACTION_ENABLED=false
MICROSERVICE_ACCOUNT_ABSTRACTION_URL=http://user-ops-indexer:8050/
# MICROSERVICE_METADATA_URL=
# MICROSERVICE_METADATA_ENABLED=
DECODE_NOT_A_CONTRACT_CALLS=true
# DATABASE_READ_ONLY_API_URL=
# ACCOUNT_DATABASE_URL=
# ACCOUNT_POOL_SIZE=
# ACCOUNT_AUTH0_DOMAIN=
# ACCOUNT_AUTH0_CLIENT_ID=
# ACCOUNT_AUTH0_CLIENT_SECRET=
# ACCOUNT_PUBLIC_TAGS_AIRTABLE_URL=
# ACCOUNT_PUBLIC_TAGS_AIRTABLE_API_KEY=
# ACCOUNT_SENDGRID_API_KEY=
# ACCOUNT_SENDGRID_SENDER=
# ACCOUNT_SENDGRID_TEMPLATE=
# ACCOUNT_VERIFICATION_EMAIL_RESEND_INTERVAL=
# ACCOUNT_PRIVATE_TAGS_LIMIT=2000
# ACCOUNT_WATCHLIST_ADDRESSES_LIMIT=15
ACCOUNT_CLOAK_KEY=
ACCOUNT_ENABLED=false
ACCOUNT_REDIS_URL=redis://redis-db:6379
EIP_1559_ELASTICITY_MULTIPLIER=2
# MIXPANEL_TOKEN=
# MIXPANEL_URL=
# AMPLITUDE_API_KEY=
# AMPLITUDE_URL=
# IPFS_GATEWAY_URL=
# ADDRESSES_TABS_COUNTERS_TTL=10m
# DENORMALIZATION_MIGRATION_BATCH_SIZE=
# DENORMALIZATION_MIGRATION_CONCURRENCY=
# TOKEN_TRANSFER_TOKEN_TYPE_MIGRATION_BATCH_SIZE=
# TOKEN_TRANSFER_TOKEN_TYPE_MIGRATION_CONCURRENCY=
# SANITIZE_INCORRECT_NFT_BATCH_SIZE=
# SANITIZE_INCORRECT_NFT_CONCURRENCY=
SOURCIFY_INTEGRATION_ENABLED=false
SOURCIFY_SERVER_URL=
SOURCIFY_REPO_URL=
SHOW_TENDERLY_LINK=false
TENDERLY_CHAIN_PATH=
# SOLIDITYSCAN_CHAIN_ID=
# SOLIDITYSCAN_API_TOKEN=
# NOVES_FI_BASE_API_URL=
# NOVES_FI_CHAIN_NAME=
# NOVES_FI_API_TOKEN=
# ZERION_BASE_API_URL=
# ZERION_API_TOKEN=
# BRIDGED_TOKENS_ENABLED=
# BRIDGED_TOKENS_ETH_OMNI_BRIDGE_MEDIATOR=
# BRIDGED_TOKENS_BSC_OMNI_BRIDGE_MEDIATOR=
# BRIDGED_TOKENS_POA_OMNI_BRIDGE_MEDIATOR=
# BRIDGED_TOKENS_AMB_BRIDGE_MEDIATORS
# BRIDGED_TOKENS_FOREIGN_JSON_RPC
# MUD_INDEXER_ENABLED=
# MUD_DATABASE_URL=
# MUD_POOL_SIZE=50

```


**`/root/blockscout/docker-compose/envs/common-frontend.env`**
```
NEXT_PUBLIC_API_HOST=fidesf1-explorer.fidesinnova.io:443
NEXT_PUBLIC_API_PROTOCOL=https
NEXT_PUBLIC_STATS_API_HOST=https://fidesf1-explorer.fidesinnova.io:443
NEXT_PUBLIC_NETWORK_NAME=FidesInnova
NEXT_PUBLIC_NETWORK_SHORT_NAME=FidesInnova
NEXT_PUBLIC_NETWORK_ID=706883
NEXT_PUBLIC_NETWORK_CURRENCY_NAME=FidesInnova
NEXT_PUBLIC_NETWORK_CURRENCY_SYMBOL=FDS
NEXT_PUBLIC_NETWORK_CURRENCY_DECIMALS=18
NEXT_PUBLIC_API_BASE_PATH=/
NEXT_PUBLIC_APP_HOST=fidesf1-explorer.fidesinnova.io:443
NEXT_PUBLIC_APP_PROTOCOL=https
NEXT_PUBLIC_HOMEPAGE_CHARTS=['daily_txs']
NEXT_PUBLIC_VISUALIZE_API_HOST=https://fidesf1-explorer.fidesinnova.io:443
NEXT_PUBLIC_IS_TESTNET=true
NEXT_PUBLIC_API_WEBSOCKET_PROTOCOL=wss
NEXT_PUBLIC_API_SPEC_URL=https://raw.githubusercontent.com/blockscout/blockscout-api-v2-swagger/main/swagger.yaml

NEXT_PUBLIC_COLOR_THEME_DEFAULT=dim
NEXT_PUBLIC_NETWORK_LOGO=https://fidesinnova.io/Download/logo-text-v2.svg
NEXT_PUBLIC_NETWORK_ICON=https://fidesinnova.io/Download/logo-text-v2.svg
NEXT_PUBLIC_FOOTER_LINKS=https://fidesinnova.io/Download/footer_links_config.json
NEXT_PUBLIC_PROMOTE_BLOCKSCOUT_IN_TITLE=false
NEXT_PUBLIC_OG_DESCRIPTION="FidesInnova revolutionizes IoT with a blockchain platform that supports automatic IoT device communication. Additionally, it features Service Contracts for seamless IoT data transfer and monetization, along with a Service Market offering a wide range of IoT services to enhance device functionalities."
NEXT_PUBLIC_AD_BANNER_PROVIDER=none
NEXT_PUBLIC_AD_TEXT_PROVIDER=none
NEXT_PUBLIC_WEB3_WALLETS=[ 'metamask' ]
NEXT_PUBLIC_WEB3_DISABLE_ADD_TOKEN_TO_WALLET=false

```

### 4-6 Build and run the Blockscout
```
cd ~/blockscout/docker-compose/
docker compose -f docker-compose.yml up --build -d
```
To stop it use
```
cd ~/blockscout/docker-compose/
docker compose -f docker-compose.yml down
```
--->
