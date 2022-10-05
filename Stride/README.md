# Stride Mainnet Node Setup Guide
* https://stride.zone

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: strided keys add <wallet name> --> strided keys add wallet

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
git clone https://github.com/Stride-Labs/stride.git && cd stride && \
git checkout v1.0.0 && \
make install
```
## Init node
```
strided init <Node Name> --chain-id stride-1
strided config chain-id stride-1
```
## Download genesis file
```
cd
wget -O $HOME/.stride/config/genesis.json "https://raw.githubusercontent.com/Stride-Labs/testnet/infra-test/poolparty/infra/genesis.json"
```
## Seeds/Peers
```
peers="84ff28824a911409e2c24f2f5ede87ae1b859b5f@5.9.147.22:26156,3a6ea526f5f0857272c79448850abef71653d4df@83.136.249.85:26656,2c324ec0f0732fffa7dd6585124b02908241f857@116.202.236.115:21016,fbc05b4136a15b0b69a2b7f093731453f04ce2f4@192.168.50.57:26656,6795c4cd27d6132a547888ed5b7996aee454a025@172.25.0.2:26656,34c52450d2f107b7c164eb103641df9e45a322d4@65.21.192.108:26656,681803f48d5e9ef9870918e8330551513eccb31c@78.47.51.53:26656,0f2d7f17589e6e31691649ec04fe19561c0d12a6@10.138.0.7:26656,cb0b38aa612e8ac05f704d9b2feb7526607afb77@159.203.191.62:26656,55b446443f2bd68e06200c3f294c735c333722b0@162.251.235.252:26656,68fb634620e00a5a18f606360b6ca6d989da8ce6@65.108.106.131:26656,f56ddd6af02efaac4c47cc8053685d11c1065996@0.0.0.0:26656,f1721dd0324f29c108c1072b2e4fe9c64e63122d@192.168.86.25:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.stride/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.stride/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.001ustdr" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.stride/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.stride/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.stride/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.strided/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.stride/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.stride/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.stride/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.stride/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.stride/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.stride/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.stride/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Stride/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/strided.service > /dev/null <<EOF
[Unit]
Description=strided
After=network-online.target

[Service]
User=$USER
ExecStart=$(which strided) start
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
sudo systemctl enable strided && \
sudo systemctl restart strided && \
sudo journalctl -u strided -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
strided status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
strided keys add <wallet name>

strided keys add <wallet name> --recover
```
## Create validator after sync
```
strided tx staking create-validator \
--amount 10000000ustdr \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(strided tendermint show-validator) \
--chain-id stride-1 \
--from <wallet name> \
--fees=200ustrd \
-y
```
### Important save priv_validator_key.json file from .stride/config/
### It is the only way to recover your validator if something happens to your the server
___
