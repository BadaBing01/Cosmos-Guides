## v.0.4.3 at height: 22300
```
sudo systemctl stop lavad
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.4.3/lavad
chmod +x lavad
mv lavad $HOME/go/bin/
sudo systemctl restart canined && sudo journalctl -u canined -f -o cat
```
