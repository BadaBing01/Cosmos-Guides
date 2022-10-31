# Shentu Mainnet Node Setup Guide
* https://www.shentu.foundation/

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: certik keys add <wallet name> --> certik keys add wallet

Example:   --details="<description>"   --> --details="Independet validator"
```

## Server preparetion
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```
## Install go 1.18.3
```
ver="1.18.3" && \
cd $HOME && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
## Clone and install binaries
```
git clone https://github.com/certikfoundation/shentu.git && cd shentu && \
git checkout v2.5.0 && \
make install

```
## Init node
```
certik init <Node Name> --chain-id shentu2.2
```
## Download genesis file
```
cd
wget -O $HOME/.certik/config/genesis.json "https://raw.githubusercontent.com/ShentuChain/mainnet/main/shentu-2.2/genesis.json"
```
## Seeds/Peers
```
peers="0393c19b176d1cf8bc560c5a8fa990301deb1a7e@95.217.126.184:26656,97e4468ac589eac505a800411c635b14511a61bb@144.76.239.24:26656,bba10290da32f3cb41e15c3a192413666ce05cee@136.243.119.240:26656,aa546f6d0d4dcae9099e852977a34904694c29e1@3.71.4.203:26656,f36a8abd833ba375029d219cb4f3e53f4dfe1e14@146.59.81.204:61656,da1b39a2a65293e40db4ac282cc0f3461bd04341@95.217.144.25:15604,d7a075977b4fcb4867f04e446730234e38459add@49.12.36.96:46656,43b923d403b569575fbee4eef1c0fb0c5d39be2f@165.232.72.33:26656,82588f011491c6100d922d133f52fc23460b9231@135.181.67.232:26656,baa8cfcad0eff850ef4e0f159bb9b4af620ae019@202.55.85.83:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.certik/config/config.toml

seeds = "3fddc0e55801f89f27a1644116e9ddb16a951e80@3.80.87.219:26656,4814cb067fe0aef705c4d304f0caa2362b7c4246@54.167.122.47:26656,f42be55f76b7d3425f493e54d043e65bfc6f43cb@54.227.66.150:26656,3edd4e16b791218b623f883d04f8aa5c3ff2cca6@shentu-seed.panthea.eu:36656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.certik/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.001uctk" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.certik/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.certik/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.certik/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.certik/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.certik/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.certik/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Shentu/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/certik.service > /dev/null <<EOF
[Unit]
Description=Certik Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which certik) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
## Run
```
sudo systemctl daemon-reload && \
sudo systemctl enable certik && \
sudo systemctl restart certik && \
sudo journalctl -u certik -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
certik status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
certik keys add <wallet name>

certik keys add <wallet name> --recover
```
## Create validator after sync
```
certik tx staking create-validator \
--amount 10000000uctk \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(certik tendermint show-validator) \
--chain-id shentu-2.2 \
--from <wallet name> \
--fees=200uctk \
-y
```
### Important save priv_validator_key.json file from .certik/config/
### It is the only way to recover your validator if something happens to your the server
___
