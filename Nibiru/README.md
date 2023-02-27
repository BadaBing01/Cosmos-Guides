# Nibiru Node Setup Guide
* https://nibiru.fi

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: nibid keys add <wallet name> --> nibid keys add wallet

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
cd $HOME && git clone https://github.com/NibiruChain/nibiru && \
cd nibiru && \
git checkout v0.19.2 && \
make install
```
## Init node
```
nibid init <Node name> --chain-id=nibiru-itn-1 --home $HOME/.nibid

nibid config chain-id nibiru-itn-1
```
## Download genesis file
```
wget -O $HOME/.nibid/config/genesis.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Nibiru/genesis.json"
```
## Peers/Seeds
```
peers="d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:39656,8fc35f8c603f2d7752ad3af0a93b12beffc556bb@144.76.30.36:15652,2ec6cb2a83c178fb490a992a3bd6a5c142c3fc61@135.181.20.30:26656,c65d746f84ef55510accfd49e6df224eb942e1f5@35.229.57.190:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml

seeds="dabcc13d6274f4dd86fd757c5c4a632f5062f817@seed-2.nibiru-testnet-2.nibiru.fi:26656,a5383b33a6086083a179f6de3c51434c5d81c69d@seed-1.nibiru-testnet-2.nibiru.fi:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nibid/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.025unibi" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.nibid/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.nibid/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.nibid/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.nibid/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.nibid/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.nibid/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Nibiru/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/nibid.service > /dev/null <<EOF
[Unit]
Description=Nibiru Network
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=$(which nibid) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Run
```
sudo systemctl daemon-reload && \
sudo systemctl enable nibid && \
sudo systemctl restart nibid && \
sudo journalctl -u nibid -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
nibid status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
nibid keys add <wallet name>

nibid keys add <wallet name> --recover
```
## Fund your wallet in [Discord](https://discord.gg/m8q6JJUvUC) or

```
FAUCET_URL="https://faucet.itn-1.nibiru.fi/"
ADDR="<Wallet Address>" 
curl -X POST -d '{"address": "'"$ADDR"'", "coins": ["10000000unibi","100000000000unusd"]}' $FAUCET_URL
```

## Create validator after sync
```
nibid tx staking create-validator \
--amount 10000000unibi \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(nibid tendermint show-validator) \
--chain-id nibiru-itn-1 \
--from <wallet name>
--fees 250unibi
-y
```
### Important save priv_validator_key.json file from .nibid/config/
### It is the only way to recover your validator if something happens to your the server
___
