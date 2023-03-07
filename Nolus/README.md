<p style="font-size:14px" align="right">


<p align="center">
 <img height="200" height="auto" src="https://i.postimg.cc/Pq5x7TGL/nolus.jpg">


# Official Links
### [Official Document](https://docs-nolus-protocol.notion.site/Run-a-Full-Node-7a92545223e7483bb4a02cce30b53aa8)
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
git clone https://github.com/Nolus-Protocol/nolus-core.git
cd nolus-core
git checkout v0.1.43
make install
```
 ## Initialisation
```python
nolusd init  <your Moniker name> --chain-id nolus-rila
nolusd config chain-id nolus-rila
``` 
## Create/recover wallet
```python
nolusd keys add <walletname>
nolusd keys add <walletname> --recover
```
## Download Genesis
```python
wget -O $HOME/.nolus/config/genesis.json "https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json"
```
## Set up the minimum gas price and Peers/Seeds
```python
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0unls\"/" $HOME/.nolus/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.nolus/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.nolus/config/config.toml
peers="56cee116ac477689df3b4d86cea5e49cfb450dda@54.246.232.38:26656,56f14005119e17ffb4ef3091886e6f7efd375bfd@34.241.107.0:26656,7f26067679b4323496319fda007a279b52387d77@63.35.222.83:26656,7f4a1876560d807bb049b2e0d0aa4c60cc83aa0a@63.32.88.49:26656,3889ba7efc588b6ec6bdef55a7295f3dd559ebd7@3.249.209.26:26656,de7b54f988a5d086656dcb588f079eb7367f6033@34.244.137.169:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nolus/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nolus/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.nolus/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.nolus/config/config.toml
```
### Pruning (Optional)
```python
sed -i -e "s/^pruning *=.*/pruning = \"nothing\"/" $HOME/.nolus/config/app.toml
```
 ### Indexer (Optional)
```python
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.nolus/config/config.toml
```
## Download addrbook
```python
wget -O $HOME/.nolus/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Nolus/addrbook.json"
``` 
 
# Create a service file
```python
tee /etc/systemd/system/nolusd.service > /dev/null <<EOF
[Unit]
Description=nolusd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nolusd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable nolusd
sudo systemctl restart nolusd && sudo journalctl -u nolusd -f -o cat
```
### Create validator
```python
 
nolusd tx staking create-validator \
 ```python
nolusd tx staking create-validator \
  --amount 1000000unls \
  --from <walletName> \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(nolusd tendermint show-validator) \
  --moniker <MonikerName>  \
  --chain-id nolus-rila \
  --identity="" \
  --details="" \
  --website="" -y
```
## Usefull commands
### Service management
Check logs
```python
journalctl -fu nolusd -o cat
```

Start service
```python
sudo systemctl start nolusd
```

Stop service
```python
sudo systemctl stop nolusd
```

Restart service
```python
sudo systemctl restart nolusd
```

### Node info
Synchronization info
```python
nolusd status 2>&1 | jq .SyncInfo
```

Validator info
```python
nolusd status 2>&1 | jq .ValidatorInfo
```

Node info
```python
nolusd status 2>&1 | jq .NodeInfo
```

Show node id
```python
nolusd tendermint show-node-id
```

### Wallet operations
List of wallets
```python
nolusd keys list
```

Recover wallet
```python
nolusd keys add wallet --recover
```

Delete wallet
```python
nolusd keys delete wallet
```

Get wallet balance
```python
nolusd query bank balances <address>
```

Transfer funds
```python
nolusd tx bank send <FROM ADDRESS> <TO_BABYLON_WALLET_ADDRESS> 10000000unls
```

### Voting
```python
nolusd tx gov vote 1 yes --from wallet --chain-id=nolus-rila
```

### Staking, Delegation and Rewards
Delegate stake
```python
nolusd tx staking delegate <babylond valoper> 10000000unls --from=wallet --chain-id=bbn-test-1 --gas=auto
```

Redelegate stake from validator to another validator
```python
nolusd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000unls --from=wallet --chain-id=nolus-rila--gas=auto
```

Withdraw all rewards
```python
nolusd tx distribution withdraw-all-rewards --from=wallet --chain-id=nolus-rila --gas=auto
```

Withdraw rewards with commision
```python
nolusd tx distribution withdraw-rewards <babylond valoper> --from=wallet --commission --chain-id=nolus-rila
```

### Validator management
Edit validator
```python
nolusd tx staking edit-validator \
  --moniker=$MONIKER \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=nolus-rila \
  --from=wallet
```

Unjail validator
```python
nolusd tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=nolus-rila \
  --gas=auto
```

### Delete node
```python
sudo systemctl stop nolusd && \
sudo systemctl disable nolusd && \
rm /etc/systemd/system/nolusd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .nolusd && \
rm -rf $(which nolusd)
```

