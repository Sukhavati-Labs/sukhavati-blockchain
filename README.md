<p align="center">
  <a href="https://sukhavati.io">
    <img alt="Sukhavati" src="https://i.imgur.com/0dNAI7O.png" width="250" />
  </a>
</p>

# Join Testnet 3C

> Notice: Testnet 3C is a fake plot testnet.
> 
> To participate in this testnet, download the latest client and follow the guide below.

## 1. Make some fake plots

```
Usage of ./skt-fake-plot:
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
$ ./skt-fake-plot -c 5000 -p /skt/plots-fake/
```

A file named `keys.json` will also be generated in the same directory. These are the Chia keys that used to create the fake plots. You will need to import the `master_sk` in `skt-farmer` when adding these plots to harvester.

```bash
$ cat /skt/fake-plots/keys.json
```

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

## 2. Start skt-farmer

```bash
Usage:
  skt-farmer [OPTIONS]

Application Options:
  -c, --configfile= Path to configuration file (config.json)
  -v, --version     Display Version information and exit
      --autostart   Generate (mine) coins when start
      --init        Init miner keystore

Help Options:
  -h, --help        Show this help message
```

`config.json` example:

```json5
{
  "app": {
    "pub_password": "yourPubPassword",
    "min_memory_block_tree_nodes": 8000
  },
  "network": {
    "p2p": {
      "seeds": "",
      "add_peer": [],
      "listen_address": "tcp://0.0.0.0:6180",
      "skip_upnp": false
    },
    "rpc": {
      "miner_grpc_port": "9687",
      "miner_http_port": "9888",
      "wallet_grpc_port": "8687",
      "wallet_http_port": "8888",
      "rpc_whitelist": [],
      "rpc_allowed_lan": [],
      "wallet_rpc_listen_ip": "127.0.0.1",
      "miner_rpc_listen_ip": "127.0.0.1"
    }
  },
  "chain": {
    "data_dir": "~/.skt/chain",
    "db_type": "leveldb"
  },
  "farmer": {
    "farmer_dir": "~/.skt/farmer",
    "payout_addr": "",
    "auto_start": false,
    "start_local_harvester": true,
    "enable_fake_plot": true,
    "enable_local_pot": false,
    "private_password": "123456"
  },
  "wallet": {
    "wallet_dir": "~/.skt/wallet",
    "autolock_seconds": 3600
  },
  "log": {
    "log_dir": "~/.skt/logs",
    "log_level": "info",
    "disable_cprint": true,
    "log_max_days": 180,
    "log_max_backups": 10,
    "log_max_size_mb": 100
  }
}
```

example:

```bash
./skt-farmer -c ~/.skt/config.json
```

## 3. Start skt-harvester

```bash
usage: skt-harvester [-h] [-v] [-c CONFIG_PATH] [--auto-shutdown] [--fake] [--debug]

harvester

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
  -c CONFIG_PATH, --config CONFIG_PATH
                        config file path path
  --auto-shutdown       allowed to be auto-shutdown by farmer
  --fake                running under fake plot mode
  --debug
```

```harvester_config.json``` example:

```json5
{
  "host": "127.0.0.1",
  "log_backup_count": 3,
  "log_dir": "/home/skt/.skt/logs",
  "log_level": "warning",
  "log_size_mb": 32,
  "name": "LocalHarvester",
  "plot_dirs": [],
  "port": 16789,
  "register_endpoint": "http://127.0.0.1:9888",
  "service_ip": "127.0.0.1"
}
```

example:

```bash
$ ./skt-harvester -c /home/skt/.skt/harvester/local_harvester_config.json
```

# RPC endpoints

```
{{wallet-url}} = 127.0.0.1:8888
{{farmer-url}} = 127.0.0.1:9688
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

## Get addresses

- Request

```bash
curl -X POST 'http://{{wallet-url}}/v1/addresses/version/0' \
-H 'Content-Type: application/json' 
```

- Response

```json5
{
    "details": [
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
    ]    
      
}
```

## Set farmer payout address 

- Request

```bash
curl -X POST 'http://{{farmer-url}}/v1/farmer/config' \
-H 'Content-Type: application/json' \
--data-raw '{
    "payout_address":"sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c"
}'
```

- Response

```json
{
    "payoutAddress": "sk1qql0pplvqn8z84333hle39fh227hkdl22a0s9vf0td0lwy57dnvuqqrrtq4c"
}
```

## Start farming

- Request

```bash
curl -X POST 'http://{{farmer-url}}/v1/farmer/start' 
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
    "hash": "e810a717ca6225825ad44d573438295993ee9901558d7010d74cfd6769ae93aa",
    "chainId": "51ed824a7c6f1f5fe9deb4e74534032f732da9735aaef61281a4c75eb3b1e421",
    "version": "1",
    "height": "26884",
    "timestamp": "1654269740",
    "posSlot": "280537",
    "previousHash": "01c1b99782c2d019b104b066d3807386dc4c1f1255b6fd6dffea61cf3e7fb433",
    "transactionRoot": "4441ebcc90959aea1b8385826b889b0952a5fcd8d66e66cad46e0be5722e90aa",
    "witnessRoot": "4441ebcc90959aea1b8385826b889b0952a5fcd8d66e66cad46e0be5722e90aa",
    "proposalRoot": "9663440551fdcd6ada50b1fa1b0003d19bc7944955820b54ab569eb9a7ab7999",
    "target": "fc54f3f9df5",
    "posPubKey": {
        "farmerPubKey": "b785108ae0cb2d4c34376d3ed93174a237d0d582a308f4778d5bbe95351703372dfaac2f155aefedac603f0ca88e11af",
        "maskPubKey": "ab62d7b9ec5132a66eaa4667d26ca22c9b1ddea1fc8647a96237a8f6fe651b08c47a7734cd16d631dd4e01c3a1305d04",
        "localPubKey": "94aad6c1748aad6d49006d6af087d39ffe1fbcf8a7a7ae32bb4bce38771b685a67fce1a8fbc087cffec56079905feb8d"
    },
    "posProof": {
        "challenge": "17b8e8ee8a27c95154e0dc4a9b743a3385007617b9f04aaf3370136625c21172",
        "proof": "2fd5a5f97f36cfd01be10c3645f8fd54b608bd9665c93f846730bac25893c24c20648a389f433ce2d0818404d838b8ce241dc1c1c42ee039d0d88609c700cf9eb19d223b542983ad66dd363600babca47ca17334e82d07dab86b7d2df1f8d6eeb0ec27ae115399ab5e7bf7e9989bc6ac7a33116c991a36953a4c0c7f1ac6e1782c056d3154d7e35e9cda5597f810194f83c2b445a9b55ecfa5d11ef825397ecd7aa2fedb8130f0852940a049a20c68543cc179afcb779f0d853e8c1575f24e070106fed0c08960fde9846400416b50f625cc80b87fbc90d623f8a2795c140b491a90523c5e6b06dd0bf8224dc7bd1d92474945b1e4db0e3af5019bf8a2caf3c0",
        "proofQuality": "1589bff6f39aaa11fe55fe041f8bb35c28c18093961cfb4b531d06e102c35a7d",
        "plotSize": 32,
        "poolPubKey": "866c192e4130b94f69a2ebd0a5a2a9c47a49c362fe52cf63dd4dac1dca92c2aa723387a2bedb83791641e741a84a2ac8",
        "plotPubKey": "9790eed2d9fe33a80e7ad0f7fb592195e9bc3d118285944ad324fd87b7b95b13ae94fa04a2e7b664f1c81b2fc4b0f9b5",
        "quality": "23551549179770",
        "plotId": "018bc73aeb2c6b3954f331de5056c5533bc96d57e06ad4785362b5b2b8526377"
    },
    "posSignature": "b7bef3d7240d218451f6f40d17a3765de96c7468a53aa5ba8758e39a954ad5e4d9c07e1895a3777988b2dd83d0ca2cf300621cec14de2b5a6ec190bc325dfd2c708f7fb53e45b78619542f0a20d6aed77672636af6e33d767cdbd647cfc5f5f2",
    "potProof": {
        "eL": "0300d0597ca601556e4e572192fc75a12e2998c80aaa04c2904d1e2dea80293c062c8956396da31670e266210243358774000201000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "proof": "03002910056f931420017c264c53d9cf41095dd88a47a5adf701a96956aad3fcfd16684a9b097b3dd9d71f255a5fbda633050201000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "iter": "5250000"
    },
    "banList": [],
    "timeUtc": "2022-06-03T15:22:20Z",
    "medianTimestamp": "1654269524"
}

```

## Get wallet balance

- Request

```bash
curl  -X POST 'http://{{wallet-url}}/v1/wallets/current/balance'
```

- Response

```json
{
    "walletId": "ac10puqqnwzve8grf9fpa3dvh27pq3sgka8rt7csr2",
    "total": "0",
    "detail": {
        "total": "0",
        "spendable": "0",
        "pendingTotal": "0",
        "pending": "0",
        "pendingChange": "0",
        "binding": "0",
        "unbinding": "0",
        "locking": "0",
        "vesting": "0",
        "withdrawableLocking": "0",
        "withdrawableUnbinding": "0",
        "withdrawableLockingUtxo": 0,
        "withdrawableUnbindingUtxo": 0
    },
    "walletSyncedHeight": "26884",
    "knownBestHeight": "26884",
    "synced": true
}
```

## Stop farming

- Request

```bash
curl -X POST 'http://{{farmer-url}}/v1/farmer/stop'
```

- Response

```json
{
    "errorCode": 0,
    "errorMessage": ""
}
```


