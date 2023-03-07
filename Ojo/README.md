# Ojo Node Setup Guide
* https://ojo.network

## Don't forget to remove those <> symbols and enter your own values
## Values in "" remain in ""
```
Example: ojod keys add <wallet name> --> ojod keys add wallet

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
cd $HOME && git clone https://github.com/ojo-network/ojo && \
cd ojod && \
git checkout v0.1.2 && \
make install
```
## Init node
```
ojod init <Node Name> --chain-id ojo-devnet
ojod config chain-id ojo-devnet
```
## Download genesis file
```
wget -O $HOME/.ojo/config/genesis.json "https://snapshots.polkachu.com/testnet-genesis/ojo/genesis.json --inet4-only"
```
## Seeds/Peers
```
peers="9c462b1c0ba63115bd70c3bd4f2935fcb93721d0@65.21.170.3:42656,a4a96019d2fbc1b5df07940cd971585311166acd@65.108.206.118:61356,ee4c5d9a8ac7401f996ef9c4d79b8abda9505400@144.76.97.251:12656,264256d32511c512a0a9d4098310a057c9999fd1@okp4.sergo.dev:12233,8d8fdad759361a57121903632adbd66ad072b1ab@okp4-testnet.nodejumper.io:29656,e3c602b146121c88d350bd7e0f6ce8977e1aacff@161.97.122.216:26656,3c805c2dead7b7a3a1d3ba2399d4d62153322413@65.108.2.41:36656,9d1482bc31fb4578a5c7f7f65c4e0aaf2dfc2336@213.239.215.77:34656,a7f1dcf7441761b0e0e1f8c6fdc79d3904c22c01@[2a02:c206:2093:4875::1]:36656,a7f1dcf7441761b0e0e1f8c6fdc79d3904c22c01@38.242.150.63:36656,99f6675049e22a0216af0e2447e7a4c5021874cd@142.132.132.200:28656,9392c27a9a561c31e7a920dc6f577d663c473ef8@154.12.225.88:26656,9928d19b7663a6fa639eb7c1ee239e671edcbdb2@5.9.147.22:26616,b5484e85a8802e0489234904d2b3a2d3c0c16e71@135.181.116.246:26106,f575a4c927d28990c84004d9af555f9956b1f22f@195.201.194.249:26656,b0b56d944cf1cc569a1e77e0923e075bad94d755@141.95.145.41:28656,667b419765454ad6970cc739e75d1ddf2b8a3f47@62.171.184.126:26656,eef77b5ae1c37f3e5809ff928c329dde906be388@65.108.133.73:21656,0f0c071ffc1948767fa866501a4d7d3144b8bf0b@142.132.208.26:26157,3ecbc8aa00b5dd8af88af7496489b0054e3b4d7f@138.68.182.247:26656,c030413e39be95c397c6681639f5d48675554c0c@51.79.78.121:26646,15fdc722cd49ef7676205b6ad3120a84728d948c@65.108.225.158:17656,61544968b65e34a59513b67613519cd37ace7ecb@161.97.151.109:26656,977373e6ff096d43c928e14724b8c6d9d7f48cb7@5.9.147.185:51656,9e5fe90a7b35285b14d27d409184026c92f43bc8@95.111.225.137:37656,1655cdc8fdfe1dc2209d47ff68c02a417ef9ed52@135.181.222.179:31656,8cdeb85dada114c959c36bb59ce258c65ae3a09c@88.198.242.163:36656,02b982c1907e461bf74d84d87c1657f12d9438b0@167.235.15.19:31656,a49302f8999e5a953ebae431c4dde93479e17155@141.95.153.244:26656,751d8d4bc73443aef9f95ddfac3572ddfc34e035@5.75.226.80:26656,f045c5324e03d54f96285a33130d3886457e18be@46.4.81.204:49656,01e492870d6c3027e9677713049e2a4de9e78e76@38.242.231.150:26656,c6e8fcf0e7545a0773cebb6445ff8e5cd3125f10@213.136.84.176:26657,da8e2423cb90fba519e685aa47669eb861ea18c4@65.108.249.79:36656,05454303ced866f3c849e4e35be4f48d96a3ece2@91.211.251.232:28656,61a8b9fdd5c21ebe6c02359cb192a4eda13d44cb@135.181.139.153:26656,bc648d1d8aef7f622d38f47ab8a0ecaee9791c46@54.36.109.62:26656,94345c725cd6e61dca339c743d800f6f497b08be@95.216.145.19:36658,0448864ede56d3c96d7d3bb8ea9f546b70cc722e@51.159.149.68:26656,08c925f04cb7a324b1aa91b472faa99c7cccc6ab@65.108.56.126:36656,339460a562c9a52b6be22da3211f19edb11faa2b@194.163.162.56:26656,2e85c1d08cfca6982c74ef2b67251aa459dd9b2f@65.109.85.170:43656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ojod/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.ojod/config/config.toml
```
## Indexing/Pruning/Minimum gas price
```
indexer="null" && \
min_retain_blocks=1 && \
minimum_gas_prices="0.00uojo" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
min_retain_blocks="1" && \
inter_block_cache="false" && \
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" /root/.ojod/config/config.toml && \
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$minimum_gas_prices\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" /root/.ojod/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" /root/.ojod/config/app.toml
sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/" /root/.ojod/config/app.toml
sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/" /root/.ojod/config/app.toml
```
## Download addrbook
```
wget -O $HOME/.ojod/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/OKP4/addrbook.json"
```
## Service file
```
sudo tee /etc/systemd/system/ojod.service > /dev/null <<EOF
[Unit]
Description=Ojo Network
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which ojod) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Run
```
sudo systemctl daemon-reload && \
sudo systemctl enable ojod && \
sudo systemctl restart ojod && \
sudo journalctl -u ojod -f -o cat
```
## Sync status
#### While synchronization is in progress, the status will be true
#### When the node is synchronized, the status will be false
```
ojod status 2>&1 | jq .SyncInfo
```
## Create/Recover wallet
### Important write this mnemonic phrase in a safe place
### It is the only way to recover your wallet if you ever forget your password
```
ojod keys add <wallet name>

ojod keys add <wallet name> --recover
```
## Fund your wallet in [Discord](https://discord.gg/cny8VYM7q3)
## Create validator after sync
```
ojod tx staking create-validator \
--amount 10000000uojo \
--moniker <validator name> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="<for validator logo>" \
--details="<description>" \
--website="" \
--pubkey $(ojod tendermint show-validator) \
--chain-id ojo-devnet \
--from <wallet name>
-y
```
### Important save priv_validator_key.json file from .ojod/config/
### It is the only way to recover your validator if something happens to your the server
___
