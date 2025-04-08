# Genesis validators guide

## Instruction

This document is written for Genesis Validators, and if you are selected a Genesis Validator participant, please be sure to double check your gentx submission deadline and the starting time of the Swisstronik mainnet. In order to submit your gentx file to the Swisstronik team, please do so after updating your binary file to the most recent version. ‘Deadline’ and the ‘Mainnet Launch’ schedule is as follows.

* _**Mainnet will launch on 10/04/2025 14:00 UTC.**_
* _**The deadline to submit your gentx is 09/04/2025 18:00 UTC.**_

_\* To submit your gentx file to the Swisstronik team, please follow the steps listed below._

## Setup Swisstronik

If you have a previous record of operating a node and hence have a `.swisstronik` folder, we recommend you delete the existing folder and start from scratch.

```
rm -rf ~/.swisstronik
```

If you previously did not operate the node or you're working on new machine, setup Intel SGX first: https://docs.swisstronik.com/swisstronik-docs/node-setup/setup-sgx/setup-intel-sgx

### Download Swisstronik most recent binary file.

```
wget https://github.com/SigmaGmbH/swisstronik-chain/releases/download/mainnet-v1.0.0/swisstronikd.zip
```

Unpack downloaded archive and configure your node as described in docs: https://docs.swisstronik.com/swisstronik-docs/node-setup/setup-node/configure-node

### Check the binary build version and the integrity before setting global command.

```
./swisstronikd version --long # Should have v1.0.0 version and ca0a97802847ac3978a569b2d9c91a96bc4376e5 commit
sha256sum ./swisstronikd # Should be 21a81717d5c3d8f620a940b45d4ee273cd4ff98e460b6729e12f22cf05d15730

sudo mv ~/swisstronikd /usr/local/bin/swisstronikd
```

### Initialize(Reset) your Swisstronik node folder using the command provided below.

```
swisstronikd init <your_moniker_name> --chain-id swisstronik_1848-1
```

### Retrieve your wallet using your mnemonic.

```
swisstronikd keys add <key_name> --recover
> Enter your bip39 mnemonic
glad music grace lawn squeeze book very text wire okay ozone morning permit tumble guard hurry various gallery kitten surprise brain piano level picture

Enter keyring passphrase: XXXXXXXX
Re-enter keyring passphrase: XXXXXXXX

- name: validator
  type: local
  address:
  pubkey: ''
  mnemonic: ""
```

If you do not have a mnemonic or if you are creating a new wallet, please use the following command.

```
swisstronikd keys add <key_name>
```

### Register Genesis account

```
swisstronikd add-genesis-account <your_wallet_address> 1000swtr
```

_**\* When registering a genesis account, for the amount field, you MUST enter 1000swtr.**_\
&#x20;  _**If a different amount was put in, please start again by resetting your folder.**_

### Create gentx file (★)

```
swisstronikd gentx <key_name> 1000swtr --chain-id swisstronik_1848-1
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--moniker <moniker_name> \
--website <website_link> \
--details <description> \
--security-contact <email>
```

### Check gentx file

If all of the above steps were completed without any error, you will be able to find a json file starting with ‘gentx-’ in the \~/.swisstronikd/config/gentx/ path.

```
{
  "body": {
    "messages": [
      {
        "@type": "/cosmos.staking.v1beta1.MsgCreateValidator",
        "description": {
          "moniker": "test-node",
          "identity": "",
          "website": "validator.com",
          "security_contact": "contact@validator.com",
          "details": "hello world\\n"
        },
        "commission": {
          "rate": "0.100000000000000000",
          "max_rate": "0.200000000000000000",
          "max_change_rate": "0.010000000000000000"
        },
        "min_self_delegation": "1",
        "delegator_address": "swtr1qvrmp6hyyveds0vgenc5hjy0qtrc76hy33fadr",
        "validator_address": "swtrvaloper1qvrmp6hyyveds0vgenc5hjy0qtrc76hy0zzxdd",
        "pubkey": {
          "@type": "/cosmos.crypto.ed25519.PubKey",
          "key": "Vlow94hC+SC2sOb0mHuoxGXG/nfzboWK5HfjFSBJE88="
        },
        "value": {
          "denom": "aswtr",
          "amount": "1000000000000000000000"
        }
      }
    ],
    "memo": "1157a6860320735421d7ef49a12b86e27727827d@192.168.20.111:26656",
    "timeout_height": "0",
    "extension_options": [],
    "non_critical_extension_options": []
  },
  "auth_info": {
    "signer_infos": [
      {
        "public_key": {
          "@type": "/cosmos.crypto.secp256k1.PubKey",
          "key": "AlnRwPXCoLNNTrFDZHmmosFwxsSB6ovibUF16zvAE7S+"
        },
        "mode_info": {
          "single": {
            "mode": "SIGN_MODE_DIRECT"
          }
        },
        "sequence": "0"
      }
    ],
    "fee": {
      "amount": [],
      "gas_limit": "200000",
      "payer": "",
      "granter": ""
    }
  },
  "signatures": [
    "cUTd4hFxYfayvvnHKF+cSUlJR+6DpC6GEb0RHsoBNmhI8WlnGa8KcRic/vMu0Zd/CGxP8B7yYKu3a+dqOpGitg=="
  ]
}
```

### Double check that you're setup is working

1. Backup your `.swisstronik` directory. By default it is located at `$HOME/.swisstronik`
```sh
cp -r $HOME/.swisstronik swisstronik-bak
```

2. Collect gentxs
```sh
swisstronikd collect-gentxs
```

3. Replace all usages of `stake` denom to `aswtr`
```sh
nano $HOME/.swisstronik/config/genesis.json
```

4. Validate genesis
```sh
swisstronikd validate-genesis
```

5. Set min gas price in app.toml
```sh
nano $HOME/.swisstronik/config/app.toml
```

6. Start the chain:
```sh
swisstronikd start
```

5. After you're confirmed that chain is running and you are validator, remove `.swisstronik` directory and restore old one from backup:
```sh
rm -r $HOME/.swisstronik
cp -r swisstronik-bak $HOME/.swisstronik
```

### Create pull request

You must submit the gentx file to the Swisstronik team as a pull request to the https://github.com/SigmaGmbH/mainnet/tree/main/gentxs/ directory.\
_**\* Please change the file name to "validator\_name.json" and submit it.**_
