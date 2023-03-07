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
peers="239caa37cb0f131b01be8151631b649dc700cd97@95.217.200.36:46656,a23cc4cbb09108bc9af380083108262454539aeb@35.215.116.65:26656,b0968b57bcb5e527230ef3cfa3f65d5f1e4647dd@35.212.224.95:26656,8671c2dbbfd918374292e2c760704414d853f5b7@35.215.121.109:26656,62fa77951a7c8f323c0499fff716cd86932d8996@65.108.199.36:24214,b6b4a4c720c4b4a191f0c5583cc298b545c330df@65.109.28.219:21656,2c40b0aedc41b7c1b20c7c243dd5edd698428c41@138.201.85.176:26696,7ee8ece35c778418302ac085817d835b67043871@116.203.245.212:26656,5af3d50dcc231884f3d3da3e3caecb0deef1dc5b@142.132.134.112:25356,9edc978fd53c8718ef0cafe62ed8ae23b4603102@136.243.103.32:36656,3d11a6c7a5d4b3c5752be0c252c557ed4acc2c30@167.235.57.142:36656,2691bb6b296b951400d871c8d0bd94a3a1cdbd52@65.109.93.152:33656,c37e444f67af17545393ad16930cd68dc7e3fd08@95.216.7.169:61156,ffe2d5ecb614762d5a1723f5f8b00d3feb6eb091@5.9.13.234:26686,7416a65de3cc548a537dbb8bdf93dbd83fe401d2@78.107.234.44:26656,cbe534c7d012e9eb4e71a5573aee8acc1adf4bc6@65.108.41.172:28056,9bcec17faba1b8f6583d37103f20bd9b968ac857@38.146.3.230:21656,fbeb2b37fe139399d7513219e25afd9eb8f81f4f@65.21.170.3:38656,ac5089a8789736e2bc3eee0bf79ca04e22202bef@162.55.80.116:29656,1145755896d6a3e9df2f130cc2cbd223cdb206f0@209.145.53.163:29656,e54b02d103f1fcf5189a86abe542670979d2029d@65.109.85.170:58656,311b3a8649a24e4a816284b92f2f3af30ac292d7@51.195.89.114:21156,408ee86160af26ee7204d220498e80638f7874f4@161.97.109.47:38656,bd35cfd5bfbea4c2a63e893860d4f9a7d880957c@213.239.217.52:45656,3aeec94e9567c66ad6bb76b496aff6d55fd53d32@65.109.171.22:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ojod/config/config.toml

seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:21656,9aa8a73ea9364aa3cf7806d4dd25b6aed88d8152@ojo-devnet.seed.mzonder.com:11556"
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
wget -O $HOME/.ojod/config/addrbook.json "https://raw.githubusercontent.com/BadaBing01/Cosmos-Chains-Sync/main/Ojo/addrbook.json"
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
