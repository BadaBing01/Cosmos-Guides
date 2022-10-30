# Jackal Mainnet Node Setup Guide
* https://jackalprotocol.com

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: canined keys add <wallet name> --> canined keys add wallet

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
git clone https://github.com/JackalLabs/canine-chain && cd canine-chain && \
git checkout v1.1.1 \
make install
```
## Init node
```
canined init <Node Name> --chain-id jackal-1
```
## Download genesis file
```
cd
wget -O $HOME/.canine/config/genesis.json "https://cdn.discordapp.com/attachments/1002389406650466405/1034968352591986859/updated_genesis2.json"
```
## Seeds/Peers
```
peers="c98028a169aabe5f8555090e6cd3370308b391b3@135.181.20.44:2506,ec38fb158ffb0272c4b7c951fc790a8f9849e280@198.244.212.27:26656,ff94a29e02de8369faf37c76d3c97684bbd51bd6@185.16.38.165:17556,39b55b1c49ad0994bbead006be40d9c84b0bf2d4@78.107.253.133:28656,f90a64a0a3f3c0480360e0fe5dd0f806d7741558@207.244.127.5:26656,57d82676ab660e8e4471664d7fee18e3e2e3dd19@89.58.38.59:26656,8be44995ab4eeafcde6e0a9e196c40d483ef6d2a@51.81.155.97:10556"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.canine/config/config.toml

seeds="ec38fb158ffb0272c4b7c951fc790a8f9849e280@198.244.212.27:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.canine/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.001ujkl" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.canine/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.canine/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.canine/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.canine/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.canine/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.canine/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Jackal/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/canined.service > /dev/null <<EOF
[Unit]
Description=canined
After=network-online.target

[Service]
User=$USER
ExecStart=$(which canined) start
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
sudo systemctl enable canined && \
sudo systemctl restart canined && \
sudo journalctl -u canined -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
canined status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
canined keys add <wallet name>

canined keys add <wallet name> --recover
```
## Create validator after sync
```
canined tx staking create-validator \
--amount 10000000ujkl \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(canined tendermint show-validator) \
--chain-id jackal-1 \
--from <wallet name> \
--fees=200ustrd \
-y
```
### Important save priv_validator_key.json file from .canine/config/
### It is the only way to recover your validator if something happens to your the server
___
