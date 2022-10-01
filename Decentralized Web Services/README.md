# DWS Node Setup Guide
* https://deweb.services/

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: dewebd keys add <wallet name> --> dewebd keys add wallet

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
git clone https://github.com/deweb-services/deweb.git && \
cd deweb && \
git checkout v0.3.1 && \
make install
```
## Init node
```
dewebd init <Node Name> --chain-id deweb-testnet-sirius
dewebd config chain-id deweb-testnet-sirius
```
## Download genesis file
```
curl -s https://raw.githubusercontent.com/deweb-services/deweb/main/genesis.json > ~/.deweb/config/genesis.json
```
## Seeds/Peers
```
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.deweb/config/config.toml

sed -E -i 's/seeds = \".*\"/seeds = \"2b1aebd0029570c20932bf7a17b3d7e67cbacc52@31.44.6.134:26656\"/' $HOME/.deweb/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.001udws" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.deweb/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.deweb/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.deweb/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.deweb/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.deweb/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.deweb/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/DWS/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/dewebd.service > /dev/null <<EOF
[Unit]
Description=deweb
After=network-online.target

[Service]
User=$USER
ExecStart=$(which dewebd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Run
```
sudo systemctl daemon-reload && \
sudo systemctl enable dewebd && \
sudo systemctl restart dewebd && \
sudo journalctl -u dewebd -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
dewebd status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
dewebd keys add <wallet name>

dewebd keys add <wallet name> --recover
```
## Fund your wallet in [Discord](https://discord.gg/NQwe4yxu)
## Create validator after sync
```
dewebd tx staking create-validator \
--amount 10000000udws \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(dewebd tendermint show-validator) \
--chain-id deweb-testnet-sirius \
--from <wallet name>
-y
```
### Important save priv_validator_key.json file from .deweb/config/
### It is the only way to recover your validator if something happens to your the server
___
