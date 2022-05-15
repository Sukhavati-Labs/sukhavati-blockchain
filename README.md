<p align="center">
  <a href="https://sukhavati.io">
    <img alt="Sukhavati" src="https://i.imgur.com/0dNAI7O.png" width="250" />
  </a>
</p>

# Testnet Phase 1
> Sukhavati Network is currently in Testnet Phase 1. 
> 
> To participate in this early testnet, you need to download the fullnode program and related tools and checkout the following guide.

## 1. Make fake plots

```
Usage of ./plot:
  -c int
    	fake k32 plot count (default 1)
  -m string
    	optional, private key:
    	which is automatically generated if you do not enter the private key
  -p string
        plot dir (default "/plots-fake")
```
example:

```bash
./plot -c 10000 -p /home/skt/plots/
```

A `keys.json` file will also be generated in the same directory.

`keys.json`:
```json
{
  "master_sk": "71de4cbc2ee7a77c043234da615ad900d33b871d56a5dd5a627e7d3b14fd6938",
  "master_pk": "a6efe9de16a8961055a25f2b0e0bab8d55e42b1dbc1cbe6194bfc4899ab3f751e7857a9a4756ec0d8ee6cbffe6b598e6",
  "pool_sk": "0d95212fba83ed43eae270a65b4e9fdcdd9905232e0c015f84a6dbf1c361ba39",
  "pool_pk": "9594b65bd93ea092de65eca8eed7b65033071f04d7221d0d16b52168f72eb5feb2beadf4d0f469fb39a228f10c913430",
  "farmer_sk": "1a2151ee6d35f8fdf99e13bac288a554efdb6a8a58ba3f04d3a88a2d5a024798",
  "farmer_pk": "845a1b209075c2d17f4630a86d039c9fd0ac4e8cf5412b9291e9ad31e775dae6080cc1ff8f036d905ee13be1ca1da38d",
  "pool_contract_puzzle_hash": "cd8298bb5c62612ef5dd8e3c497a11a9de476448de683a1d64cd4179f5cbde0d"
 }
```

## 2. Start harvester

```bash
Usage of ./harvester-cli:
  -c string
    	config file path
  -l string
    	listen address (default "127.0.0.1:8000")
  -log string
    	log path (default "/var/log/harvester")
  -p value
    	plot files dir path
  -v string
        loglevel value can be: panic, fatal, error, warn, info, debug, trace (default "debug")
```

example:

```bash
./harvester-cli -log ./logs -p /home/skt/plots/
```

## 3. Start miner

```bash
Usage:
  minerd [OPTIONS]

Application Options:
  -C, --configfile= Path to configuration file (config.json)
  -V, --version     Display Version information and exit
  -P, --privpass=   Private passphrase for miner
```

example:

```bash
./minerd -C config.json -P 12345678
```

`config.json` example:

```json
{
  "app": {
    "pub_password": "123456"
  },
  "network": {
    "p2p": {
      "listen_address": "tcp://0.0.0.0:43458",
      "skip_upnp": true
    },
    "rpc": {
      "miner_grpc_port": "9687",
      "miner_http_port": "9688",
      "wallet_grpc_port": "8587",
      "wallet_http_port": "8888",
      "api_whitelist": [],
      "api_allowed_lan": []
    }
  },
  "log": {
    "log_level": "info",
    "disable_cprint": true
  },
  "miner": {
    "master_sk": "71de4cbc2ee7a77c043234da615ad900d33b871d56a5dd5a627e7d3b14fd6938",
    "harvester_addr" : [
      "http://127.0.0.1:8000"
    ],
    "mining_addr": "sk1qqft78jcfy2j8pnts4nvzs6m4ptx2da5mcymrk3fuqh4qls3s0cxvq27957c",
    "generate": false,
    "allow_solo": true,
    "enable_local_vdf": true,
    "private_password": "12345678"
  }
}
```

# RPC endpoints

```
{{wallet-url}} = 127.0.0.1:8888
{{miner-url}} = 127.0.0.1:9688
```

## Create wallet

- Request

```bash
curl -X POST 'http://{{wallet-url}}/v1/wallets/create' \
-H 'Content-Type: application/json' \
--data-raw '{
	"passphrase":"123456789",
	"remarks":"skt"
}'
```

- Response

```json
{
	"walletId":"ac10an6q8j9fnk9x204xq9q8mc353ks6y5zgeu79d6", 
	"mnemonic":"upon mutual shoulder believe monkey arctic major axis mammal ring lizard bronze"
}
```

## Use wallet

- Request

```bash
curl -X POST 'http://{{wallet-url}}/v1/wallets/use' \
-H 'Content-Type: application/json' \
--data-raw '{
	"wallet_id":"ac10an6q8j9fnk9x204xq9q8mc353ks6y5zgeu79d6"
}'
```

- Response

```json
{
  "chainId": "c9bab70fd72be9cf6819c96aa09a3450faca169a393e11b9b3f8654bda9463d8",
  "walletId": "ac10an6q8j9fnk9x204xq9q8mc353ks6y5zgeu79d6",
  "type": 1,
  "totalBalance": "0",
  "externalKeyCount": 0,
  "internalKeyCount": 0,
  "remarks": "skt"
}

```

## Create addresses

- Request

```bash
curl -X POST 'http://{{wallet-url}}/v1/addresses/create' \
-H 'Content-Type: application/json' 
```

- Response

```json5
{
  "address": "sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c",
  "version": 0,
  "used": false,
  "stdAddress": "sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c",
  "scriptAddress": "fbc21fb013388f58c637fe6254dd4af5ecdfa95d7c0ac4bd6d7fdc4a79b36700",
  "redeemScript": null,
  "publicKey": "03c6118fda58a20879bd92f42746a6c8945dfc883f92230fe8548aac4defb76a59",
  "addressClass": 0
}
```

## Set miner payout address 

- Request

```bash
curl -X POST 'http://{{miner-url}}/v1/miner/config' \
-H 'Content-Type: application/json' \
--data-raw '{
    "payout_address":"sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c",
    "passphrase":"12345678"
}'
```

- Response

```json
{
    "payoutAddress": "sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c"
}
```

## Start mining

- Request

```bash
curl -X POST 'http://{{miner-url}}/v1/miner/start' 
```

- Response

```json
{
    "errorCode": 0,
    "errorMessage": ""
}
```

## Get best block

- Request

```bash
curl -X GET 'http://{{wallet-url}}/v1/blocks/best' 
```

- Response

```json
{
  "height": "8792",
  "target": "4f02aaa92a9",
  "posProof": {
    "challenge": "1c548032f3f8c669ddba0b9fd52854e390705f5132749844f3b4b0693ebafa45",
    "proof": "b23af2ed86a805bcd52100f9f413707c1f42247c1619cd52aab55c8ad8913902f43312dd1fe138a05128469ed4720463070dbd381f3c8bc9cbe941fafa52987adf9ddeb0e9c15d9f013676dfa667f938888c827aed54f99cc462e90a341ea16998b288d8a93bdb31bf67e279459d9d4c75192597dee88e7ca640dc15cff06698fc2f3a4ce34c74f874860579b22e937c71a0cc88bd2d733dbffa7925b1f2a1714cc18db43abc46afa4646f3f315d89de54f022dd4ebb4754191dcdeed0c8ce3af49a207d398dd4191170ef332890d291da486f018724536416e6537126e703b23b2bef7d52f434e242d105081b5173b157fe74ab06c4520c12aa14ad02b697d2",
    "proofQuality": "a42457f757be2c7db4c6d2500ac03d9b10ea051eed13f4ae77eacda477896ddd",
    "plotSize": 32,
    "plotPubKey": "aa3bdb537c4c02ec9a784b226423e8827af1e0803d8d399e1b09d46a353096a561c9f971e8898bdffee725c42d001b3e",
    "quality": "c7a1c6859cd"
  }
}
```

## Get wallet current balance

- Request

```bash
curl  -X POST 'http://{{wallet-url}}/v1/wallets/current/balance'
```

- Response

```json
{
    "walletId": "ac10an6q8j9fnk9x204xq9q8mc353ks6y5zgeu79d6",
    "total": "64537.27084536",
    "detail": {
        "spendable": "64537.27084536",
        "withdrawableLocking": "0",
        "withdrawableBinding": "0"
    }
}
```

## Stop mining

- Request

```bash
curl -X POST 'http://{{miner-url}}/v1/miner/stop'
```

- Response

```json
{
    "errorCode": 0,
    "errorMessage": ""
}
```

