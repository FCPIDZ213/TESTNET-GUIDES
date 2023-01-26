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
 
## Create/recover wallet
```python
planqd keys add <walletname>
planqd keys add <walletname> --recover
