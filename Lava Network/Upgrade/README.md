## v.0.5.2 at height: 63760
```
cd $HOME/lava
git fetch --all
git checkout v0.5.2
make install
lavad version --long | head
sudo systemctl restart lavad && sudo journalctl -u lavad -f -o cat
```
