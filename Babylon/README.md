<p style="font-size:14px" align="right">


<p align="center">
 <img height="200" height="auto" src="https://i.postimg.cc/fRDpg1cX/Babylon.jpg">


# Official Links
### [Official Document](https://docs.babylonchain.io/docs/installation)
### [Babylon Official Discord](https://discord.gg/babylonchain)

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Tetsnet   |   4|  8GB | 500GB    |

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
git checkout v0.4.0
make install
```
  ## Initialisation
```python
babylond init <your Moniker name> --chain-id bbn-test-1
babylond config chain-id bbn-test-1
``` 
## Create/recover wallet
```python
babylond keys add <walletname>
babylond keys add <walletname> --recover
```
 
