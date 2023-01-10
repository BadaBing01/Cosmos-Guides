# Lava Network Node Setup Guide
* https://lavanet.xyz

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: lavad keys add <wallet name> --> lavad keys add wallet

Example:   --details="<description>"   --> --details="Independet validator"
```

## Server preparetion
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```
## Install go 1.18.3
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
cd $HOME && \
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T.git && \
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.4.0/lavad && \
chmod +x lavad && \
mv lavad $HOME/go/bin/
```
## Init node
```
lavad init <Node Name> --chain-id lava-testnet-1
```
## Download genesis file
```
cp $HOME/GHFkqmTzpdNLDd6T/testnet-1/genesis_json/genesis.json $HOME/.lava/config
```
## Seeds/Peers
```
peers="9872ab6a76199fcbeca1f7f791755e726aa97686@116.202.165.116:11656,f6a3fcd1910ab808192c4c40a29fa0e85cd874cd@52.18.46.103:26656,525696e557db51c4d5f5bca1d7152753c7426c2e@34.192.150.110:26656,f0758765ef0350d5cbbdeebf0b8e84f76e21c46d@54.221.204.97:26656,3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.05ulava" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.lava/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.lava/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.lava/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.lava/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.lava/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.lava/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Lava%20Network/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=noisd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lavad) start
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
sudo systemctl enable noisd && \
sudo systemctl restart lavad && \
sudo journalctl -u lavad -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
lavad status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
lavad keys add <wallet name>

lavad keys add <wallet name> --recover
```
## Fund your wallet in [Discord](https://discord.gg/9XSq4Axmjj)
## Create validator after sync
```
lavad tx staking create-validator \
--amount 10000000ulava \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(lavad tendermint show-validator) \
--chain-id lava-testnet-1 \
--from <wallet name>
-y
```
### Important save priv_validator_key.json file from .lava/config/
### It is the only way to recover your validator if something happens to your the server
___
