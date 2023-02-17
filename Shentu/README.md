# Shentu Mainnet Node Setup Guide
* https://www.shentu.foundation/

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: shentud keys add <wallet name> --> shentud keys add wallet

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
git clone https://github.com/certikfoundation/shentu.git && cd shentu && \
git checkout v2.6.0 && \
make install

```
## Init node
```
shentud init <Node Name> --chain-id shentu2.2
```
## Download genesis file
```
cd
wget -O $HOME/.certik/config/genesis.json "https://raw.githubusercontent.com/ShentuChain/mainnet/main/shentu-2.2/genesis.json"
```
## Seeds/Peers
```
peers=b0690ba7a9cf755fc46486d45b5fd30d0da443de@78.47.143.61:26656,5b73f98db91d006f7da1db22244bc316f6b3742e@167.86.69.137:26656,bba10290da32f3cb41e15c3a192413666ce05cee@136.243.119.240:26656,471518432477e31ea348af246c0b54095d41352c@78.47.210.208:26656,41fe2472fa15299065af6a3d1cb97b910166d702@65.108.78.167:11756,100aee4f6928d09e3dddfd0c5028cf127509bbd9@162.55.132.48:15607,8a210f1bcfc9015a7bc18dcc5add29c0dce3f2dc@135.181.173.64:26656,db927f396ebc0cef65729961c732a19821834226@141.95.98.27:26656,897b95141cb391d69dd8a49cb36ec8c7a7ae9981@5.9.97.174:15607,6cceba286b498d4a1931f85e35ea0fa433373057@78.47.208.96:26656,eeea9639f366d7184db0f1ad60122957da82d734@135.181.113.227:2407,ccb2ecbb3897a39a5b5aa945c343591c7f582bee@23.88.69.22:28356,0494d17e2cbe835c7e85a073c7c4f0b6dc17d834@31.7.207.245:27756,207c47bed435e4174844064ef3f51ca35b059de2@194.163.172.37:26656,a605e6fa81adf6e510da9a819103e4244d97cdff@54.241.84.226:26656,fa2f75920fcea2589587fa2ff9a661b498b3c5b7@195.201.172.9:15607,40d3832c2f6409e039c01ab9494c7d705fe54dc8@213.136.80.20:26656,9023d9a3d60f147514129aabe6f6b60cfa4ee128@194.195.213.37:26656,e1754812621b14c4a993dd354a85421538284da4@89.58.59.44:26656,2ab182095835f907bb351c020c56aafaf662b93c@149.102.148.1:26666,06374bc14ea48a3ac4ad29258ba252739bf240f5@85.10.193.146:26656,3fddc0e55801f89f27a1644116e9ddb16a951e80@3.80.87.219:26656,4814cb067fe0aef705c4d304f0caa2362b7c4246@54.167.122.47:26656,f42be55f76b7d3425f493e54d043e65bfc6f43cb@54.227.66.150:26656,207c47bed435e4174844064ef3f51ca35b059de2@shentu-peer.panthea.eu:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.shentud/config/config.toml

seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:14056"
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
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.shentud/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.shentud/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.shentud/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.shentud/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.shentud/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.certik/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Shentu/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/shentud.service > /dev/null <<EOF
[Unit]
Description=Shentud Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which shentud) start --home /root/.shentud
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
sudo systemctl enable shentud && \
sudo systemctl restart shentud && \
sudo journalctl -u shentud -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
shentud status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
shentud keys add <wallet name>

shentud keys add <wallet name> --recover
```
## Create validator after sync
```
shentud tx staking create-validator \
--amount 10000000uctk \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(shentud tendermint show-validator) \
--chain-id shentu-2.2 \
--from <wallet name> \
--fees=200uctk \
-y
```
### Important save priv_validator_key.json file from .shentud/config/
### It is the only way to recover your validator if something happens to your the server
___
