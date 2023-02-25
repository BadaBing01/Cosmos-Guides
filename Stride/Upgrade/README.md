## v6.0.0 at height: 2559552
```
cd $HOME/stride
git fetch --all
git checkout v6.0.0
make install
strided version
sudo systemctl restart strided && journalctl -u strided -f -o cat
```
