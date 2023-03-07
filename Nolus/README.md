<p style="font-size:14px" align="right">


<p align="center">
 <img height="200" height="auto" src="https://i.postimg.cc/Pq5x7TGL/nolus.jpg">


# Official Links
### [Official Document] (https://docs-nolus-protocol.notion.site/Run-a-Full-Node-7a92545223e7483bb4a02cce30b53aa8)
### [Babylon Official Discord](https://discord.gg/nolus-protocol)

# Explorer
### [Explorer](https://explorer-rila.nolus.io/nolus-rila)

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Tetsnet   |   4|  8GB | 150GB    |

 ### Preparing the server

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.20

```bash
ver="1.20" && \
wget "https://go.dev/dl/go1.20.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
# Build 
```python
cd $HOME
git clone https://github.com/babylonchain/babylon.git
cd babylon
git checkout v0.5.0
make install
```
 ## Initialisation
```python
babylond init  <your Moniker name> --chain-id bbn-test-1
babylond config chain-id bbn-test-1
``` 
## Create/recover wallet
```python
babylond keys add <walletname>
babylond keys add <walletname> --recover
```
## Download Genesis
```python
coming soon
```
## Set up the minimum gas price and Peers/Seeds
```python
coming soon
```
### Pruning (Optional)
```python
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.babylond/config/app.toml
```
 ### Indexer (Optional)
```python
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.babylond/config/config.toml
```
# Create a service file
```python
sudo tee /etc/systemd/system/babylond.service > /dev/null <<EOF
[Unit]
Description=babylon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which babylond) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable babylond
sudo systemctl restart babylond && sudo journalctl -u planqd -f -o cat
```
### Create validator
```python
 
babylond tx staking create-validator \
  --amount=10000000ubbn \
  --moniker=<your Moniker name> \
  --from=<walletName> \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --pubkey=$(babylond tendermint show-validator) \
  --gas="1000000" \
  --gas-prices="0.0025ubbn" \
  --gas-adjustment="1.2" \
  --chain-id=bbn-test-1 \
  --identity="" \
  --details="" \
  --website="" -y
  ```
## Usefull commands
### Service management
Check logs
```python
journalctl -fu babylond -o cat
```

Start service
```python
sudo systemctl start babylond
```

Stop service
```python
sudo systemctl stop babylond
```

Restart service
```python
sudo systemctl restart babylond
```

### Node info
Synchronization info
```python
babylond status 2>&1 | jq .SyncInfo
```

Validator info
```python
babylond status 2>&1 | jq .ValidatorInfo
```

Node info
```python
babylond status 2>&1 | jq .NodeInfo
```

Show node id
```python
babylond tendermint show-node-id
```

### Wallet operations
List of wallets
```python
babylond keys list
```

Recover wallet
```python
babylond keys add wallet --recover
```

Delete wallet
```python
babylond keys delete wallet
```

Get wallet balance
```python
babylond query bank balances <address>
```

Transfer funds
```python
babylond tx bank send <FROM ADDRESS> <TO_BABYLON_WALLET_ADDRESS> 10000000ubbn
```

### Voting
```python
babylond tx gov vote 1 yes --from wallet --chain-id=bbn-test-1
```

### Staking, Delegation and Rewards
Delegate stake
```python
babylond tx staking delegate <babylond valoper> 10000000ubbn --from=wallet --chain-id=bbn-test-1 --gas=auto
```

Redelegate stake from validator to another validator
```python
babylond tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000ubbn --from=wallet --chain-id=bbn-test-1--gas=auto
```

Withdraw all rewards
```python
babylond tx distribution withdraw-all-rewards --from=wallet --chain-id=bbn-test-1 --gas=auto
```

Withdraw rewards with commision
```python
babylond tx distribution withdraw-rewards <babylond valoper> --from=wallet --commission --chain-id=bbn-test-1
```

### Validator management
Edit validator
```python
babylond tx staking edit-validator \
  --moniker=$MONIKER \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=bbn-test-1 \
  --from=wallet
```

Unjail validator
```python
babylond tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=bbn-test-1 \
  --gas=auto
```

### Delete node
```python
sudo systemctl stop babylond && \
sudo systemctl disable babylond && \
rm /etc/systemd/system/babylond.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .babylond && \
rm -rf $(which babylond)
```

