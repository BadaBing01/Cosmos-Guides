## v.1.1.2 at height: 118040
```
sudo systemctl stop canined
cd $HOME && rm -rf canine-chain
git clone https://github.com/JackalLabs/canine-chain.git
cd canine-chain
git checkout v1.2.0
make build
sudo mv ./build/canined $(which canined)
sudo systemctl restart canined && sudo journalctl -u canined -f -o cat
```
