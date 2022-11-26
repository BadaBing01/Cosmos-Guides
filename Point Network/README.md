# Point Network Mainnet Node Setup Guide
* https://pointnetwork.io

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: pointd keys add <wallet name> --> pointd keys add wallet

Example:   --details="<description>"   --> --details="Independet validator"
```

## Server preparetion
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```
## Install go 1.19
```
ver="1.19" && \
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
git clone https://github.com/pointnetwork/point-chain.git && \
cd point-chain && \
git checkout mainnet && \
make install

```
## Init node
```
pointd init <Node Name> --chain-id point_10687-1
```
## Download genesis file
```
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/mainnet-1/genesis.json

wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/mainnet-1/config.toml

mv config.toml genesis.json ~/.pointd/config/
```
## Peers
```
peers="8673c1f04c29c464189e8bf29e51fb0b38da2f19@rpc-mainnet-1.point.space:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.pointd/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.001apoint" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.pointd/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.pointd/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.pointd/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.pointd/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.pointd/config/app.toml
```
## State Sync
* https://docs.pointnetwork.io/docs/point_chain/validators/setup/statesync

## Download addrbook
```
wget -O $HOME/.pointd/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Point%20Network/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/pointd.service > /dev/null <<EOF
[Unit]
Description=Point Network
After=network-online.target

[Service]
User=$USER
ExecStart=$(which pointd) start
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
sudo systemctl enable pointd && \
sudo systemctl restart pointd && \
sudo journalctl -u pointd -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
pointd status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
pointd keys add <wallet name>

pointd keys add <wallet name> --recover
```
## Create validator after sync
```
pointd tx staking create-validator \
--amount 10000000apoint \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(pointd tendermint show-validator) \
--chain-id point_10687-1 \
--from <wallet name> \
--fees=250apoint \
-y
```
### Important save priv_validator_key.json file from .pointd/config/
### It is the only way to recover your validator if something happens to your the server
___
