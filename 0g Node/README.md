
# Official Links
### [Official Document](https://blog.0g.ai/blog/testnet-node-operator-and-validator-application-guide)
### [0glabs Official Discord](https://discord.gg/0glabs)



- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Tetsnet       |   8  | 64G     |    1TB    |

 ### Preparing the server

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.22

```bash
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```
# Build 
```python
cd $HOME
git clone -b v0.1.0 https://github.com/0glabs/0g-chain.git
cd 0g-chain
make install
0gchaind version
```
 ## Initialisation
```python
0gchaind init  <Monikername> --chain-id zgtendermint_16600-1
0gchaind config chain-id zgtendermint_16600-1
``` 
## Create/recover wallet
```python
0gchaind keys add <walletname>
0gchaind keys add DZ-staking --recover
```
## Download Genesis
```python
wget https://github.com/0glabs/0g-chain/releases/download/v0.1.0/genesis.json -O $HOME/.0gchain/config/genesis.json
```
##Seeds and peers 
```python
SEEDS="c4d619f6088cb0b24b4ab43a0510bf9251ab5d7f@54.241.167.190:26656,44d11d4ba92a01b520923f51632d2450984d5886@54.176.175.48:26656,f2693dd86766b5bf8fd6ab87e2e970d564d20aff@54.193.250.204:26656,f878d40c538c8c23653a5b70f615f8dccec6fb9f@54.215.187.94:26656" && \
sed -i.bak -e "s/^seeds *=.*/seeds = \"${SEEDS}\"/" $HOME/.0gchain/config/config.toml
```
## Set up gas price 
```python
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" $HOME/.0gchain/config/app.toml
```
### Pruning (Optional)
```python
sed -i.bak -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" $HOME/.0gchain/config/app.toml
```
 ### Indexer (Optional)
```python
indexer="null" && \
sed -i "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.0gchain/config/config.toml
```
# Create a service file
```python
sudo tee /etc/systemd/system/ogd.service > /dev/null <<EOF
[Unit]
Description=OG Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Start
```python
sudo systemctl daemon-reload
sudo systemctl restart ogd && sudo journalctl -u ogd -f -o cat
```
### Create validator
```python
 
0gchaind tx staking create-validator \
  --amount=1000000ua0gi \
  --pubkey=$(0gchaind tendermint show-validator) \
  --moniker=$MONIKER \
  --chain-id=$CHAIN_ID \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --min-self-delegation=1 \
  --from=$WALLET_NAME \
  --identity="" \
  --website="" \
  --details="0G to the moon!" \
  --gas=500000 --gas-prices=99999ua0gi \
  -y
  ```
## Usefull commands
### Service management
Check logs
```python
journalctl -fu 0gchaind  -o cat
```

Start service
```python
sudo systemctl start 0gchaind 
```

Stop service
```python
sudo systemctl stop 0gchaind 
```

Restart service
```python
sudo systemctl restart 0gchaind 
```

### Node info
Synchronization info
```python
0gchaind  status 2>&1 | jq .SyncInfo
```

Validator info
```python
0gchaind  status 2>&1 | jq .ValidatorInfo
```

Node info
```python
0gchaind  status 2>&1 | jq .NodeInfo
```

Show node id
```python
0gchaind  tendermint show-node-id
```

### Wallet operations
List of wallets
```python
0gchaind keys list
```

Recover wallet
```python
0gchaind keys add wallet --recover
```

Delete wallet
```python
0gchaind keys delete wallet
```

Get wallet balance
```python
0gchaind query bank balances <address>
```

Transfer funds
```python
0gchaind tx bank send $WALLET_NAME <TO_WALLET> <AMOUNT>ua0gi --gas=500000 --gas-prices=99999ua0gi -y
```

### Voting
```python
0gchaind tx gov vote 1 yes --from wallet --chain-id=bbn-test-1
```

### Staking, Delegation and Rewards
Delegate stake
```python
0gchaind tx staking delegate $(0gchaind keys show $WALLET_NAME --bech val -a)  <AMOUNT>ua0gi --from $WALLET_NAME --gas=500000 --gas-prices=99999ua0gi -y
```

### Validator management
Edit validator
```python
0gchaind tx staking edit-validator \
  --moniker=$MONIKER \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=zgtendermint_16600-1 \
  --from=wallet
```

Unjail validator
```python
0gchaind tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=zgtendermint_16600-1 \
  --gas=auto
```

### Delete node
```python
sudo systemctl stop 0gchaind && \
sudo systemctl disable 0gchaind && \
rm /etc/systemd/system/0gchaind.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .0gchaind && \
rm -rf $(which 0gchaind)
```
