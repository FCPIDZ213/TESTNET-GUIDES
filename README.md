<p style="font-size:14px" align="right">


<p align="center">
 <img height="200" height="auto" src="https://user-images.githubusercontent.com/34649601/209750989-677f97ae-6b49-4e19-8594-5846871a9aef.png">


# Official Links
### [Official Document](https://docs.planq.network/validators/overview.html)
### [Planq Official Discord](https://discord.gg/planq-network)

# Explorer
### [Explorer](https://explorer.dz-staking.com/planq/staking/plqvaloper1rakhw504djts8xw64g38qzayzwhec4seuajy2g)

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Tetsnet   |   4|  8GB | 500GB    |

 ### Preparing the server

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.19

```bash
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
# Build 
```python
git clone https://github.com/planq-network/planq.git
cd planq
git fetch
git checkout v1.0.3
make install
```
 ## Initialisation
```python
planqd init <your Moniker name> --chain-id=planq_7070-2
planqd config chain-id planq_7070-2
``` 
## Create/recover wallet
```python
planqd keys add <walletname>
planqd keys add <walletname> --recover
```
## Download Genesis
```python
wget https://raw.githubusercontent.com/planq-network/networks/main/mainnet/genesis.json
mv genesis.json ~/.planqd/config/
```
## Set up the minimum gas price and Peers/Seeds
```python
SEEDS="dd2f0ceaa0b21491ecae17413b242d69916550ae@135.125.247.70:26656,0525de7e7640008d2a2e01d1a7f6456f28f3324c@51.79.142.6:26656,21432722b67540f6b366806dff295849738d7865@139.99.223.241:26656" 
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.planqd/config/config.toml
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025aplanq\"|" $HOME/.planqd/config/app.toml
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"5s\"/" $HOME/.planqd/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 120/g' $HOME/.planqd/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 60/g' $HOME/.planqd/config/config.toml
```
### Pruning (Optional)
```python
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.planqd/config/app.toml
```
 ### Indexer (Optional)
```python
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.planqd/config/config.toml
```
# Create a service file
```python
sudo tee /etc/systemd/system/planqd.service > /dev/null <<EOF
[Unit]
Description=planq
After=network-online.target

[Service]
User=$USER
ExecStart=$(which planqd) start
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
sudo systemctl enable planqd
sudo systemctl restart planqd && sudo journalctl -u planqd -f -o cat
```
### Create validator
```python
 
planqd tx staking create-validator \
  --amount=1000000000000aplanq \
  --moniker=<your Moniker name> \
  --from=<walletName> \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --pubkey=$(planqd tendermint show-validator) \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --gas-adjustment="1.15" \
  --chain-id=planq_7070-2 \
  --identity="" \
  --details="" \
  --website="" -y
  ```
## Usefull commands
### Service management
Check logs
```python
journalctl -fu planqd -o cat
```

Start service
```python
sudo systemctl start planqd
```

Stop service
```python
sudo systemctl stop planqd
```

Restart service
```python
sudo systemctl restart planqd
```

### Node info
Synchronization info
```python
planqd status 2>&1 | jq .SyncInfo
```

Validator info
```python
planqd status 2>&1 | jq .ValidatorInfo
```

Node info
```python
planqd status 2>&1 | jq .NodeInfo
```

Show node id
```python
planqd tendermint show-node-id
```

### Wallet operations
List of wallets
```python
planqd keys list
```

Recover wallet
```python
planqd keys add wallet --recover
```

Delete wallet
```python
planqd keys delete wallet
```

Get wallet balance
```python
planqd query bank balances <address>
```

Transfer funds
```python
planqd tx bank send <FROM ADDRESS> <TO_planq_WALLET_ADDRESS> 10000000aplanq
```

### Voting
```python
planqd tx gov vote 1 yes --from wallet --chain-id=planq_7070-2
```

### Staking, Delegation and Rewards
Delegate stake
```python
planqd tx staking delegate <planq valoper> 10000000aplanq --from=wallet --chain-id=planq_7070-2 --gas=auto
```

Redelegate stake from validator to another validator
```python
planqd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000aplanq --from=wallet --chain-id=planq_7070-2 --gas=auto
```

Withdraw all rewards
```python
planqd tx distribution withdraw-all-rewards --from=wallet --chain-id=planq_7070-2 --gas=auto
```

Withdraw rewards with commision
```python
planqd tx distribution withdraw-rewards <planq valoper> --from=wallet --commission --chain-id=planq_7070-2
```

### Validator management
Edit validator
```python
planqd tx staking edit-validator \
  --moniker=$MONIKER \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=planq_7070-2 \
  --from=wallet
```

Unjail validator
```python
planqd tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=planq_7070-2 \
  --gas=auto
```

### Delete node
```python
sudo systemctl stop planqd && \
sudo systemctl disable planqd && \
rm /etc/systemd/system/planqd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .planqd && \
rm -rf $(which planqd)
```
