<p style="font-size:14px" align="right">


<p align="center">
 <img height="200" height="auto" src="https://i.postimg.cc/x85RFbZf/wadren1.jpg">











# Official Links
### [Official Document](https://docs.wardenprotocol.org/)
### [0glabs Official Discord](https://discord.gg/gEdhUMw7)



- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Tetsnet       |  4   |    8G    |    600     |

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
git clone https://github.com/warden-protocol/wardenprotocol
cd  wardenprotocol
git checkout v0.3.0
make install
wardend version version
```
 ## Initialisation
```python
wardend init  <Monikername> --chain-id buenavista-1
wardend config chain-id buenavista-1
``` 
## Create/recover wallet
```python
wardend keys add <walletname>
wardend keys add<walletname> --recover
```
## Download Genesis
```python
wget https://rpc-warden-testnet.trusted-point.com/genesis.json -O $HOME/.warden/config/genesis.json
```
##Seeds and peers 
```python
PEERS="3f6e3d6f99c23487e797d28eb9d95e3cfb0b6e61@84.247.147.57:26656,22df256e71ba01bba80038c527a4f1103ad129d9@65.108.251.125:26656,ee528080741055cb7067f3e0bdda9badac834fc5@81.0.249.86:11256,ff13a301d956badc596de7145b19052f0965d62d@44.205.14.73:666,e9776f41f1682012555b86a3353db46188e19bc3@173.249.39.125:26656,89fbdc8995727e1c0d8525cdf087f1a6517f2685@168.119.10.134:29482,8973f6b31d3183daca419415a424604a5f67b8bd@138.201.51.154:30504,d4af4ec2657c9756c87aa5b49d2d724b45f96d8b@188.165.228.73:26656" && \
SEEDS="" && \
sed -i \
    -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" \
    -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" \
    "$HOME/.warden/config/config.toml"
```
## Set up gas price 
```python
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01uward\"/" $HOME/.warden/config/app.toml
```
### Pruning (Optional)
```python
sed -i \
    -e "s/^pruning *=.*/pruning = \"custom\"/" \
    -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" \
    -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" \
    "$HOME/.warden/config/app.toml"
```
 ### Indexer (Optional)
```python
indexer="null" && \
sed -i "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.warden/config/config.toml
```
# Create a service file
```python
sudo tee /etc/systemd/system/warden.service > /dev/null <<EOF
[Unit]
Description=Warden Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which wardend) start --home $HOME/.warden
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Start
```python
sudo systemctl daemon-reload
sudo systemctl restart wardend && sudo journalctl -u wardend-f -o cat
```
##Create JSON file
```python
pubkey=$(wardend tendermint show-validator)
  amount="1000000uward"
  moniker="<your Moniker name>"
  from="<walletName>"
  commission-rate="0.05" 
  commission-max-rate="0.20" 
  commission-max-change-rate="0.01" 
  min-self-delegation="1" 
  identity="" 
  details="" 
   ```
##Create validator
```python
 wardend tx staking create-validator $HOME/.warden/config/validator.json \
  --chain-id=buenavista-1 \
  --fees 30000uward \
  --gas 2000000 \
  --from <walletName> \
  -y

   ```
## Usefull commands
### Service management
Check logs
```python
journalctl -fu wardend -o cat
```

Start service
```python
sudo systemctl start wardend
```

Stop service
```python
sudo systemctl stop wardend
```

Restart service
```python
sudo systemctl restart wardend
```

### Node info
Synchronization info
```python
wardend status 2>&1 | jq .SyncInfo
```

Validator info
```python
wardend status 2>&1 | jq .ValidatorInfo
```

Node info
```python
wardend status 2>&1 | jq .NodeInfo
```

Show node id
```python
wardend tendermint show-node-id
```

### Wallet operations
List of wallets
```python
wardend keys list
```
Recover wallet
```python
wardend keys add wallet --recover
```
Delete wallet
```python
wardend keys delete wallet
```
Get wallet balance
```python
wardend query bank balances <address>
```
### Staking, Delegation and Rewards
Delegate stake
```python
wardend tx staking delegate $(wardend keys show <walletName> --bech val -a)  <AMOUNT>uward --from <walletName> --gas 2000000 --fees 30000uward -y
```
###Unjail validator
```python
wardend tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=buenavista-1 \
  --gas=auto
```

### Delete node
```python
sudo systemctl stop wardend && \
sudo systemctl disable wardend && \
rm /etc/systemd/system/wardend.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .wardend && \
rm -rf $(which wardend)
```
