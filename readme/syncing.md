# Syncing

**Download snapshort:**

Copy

```
sudo systemctl stop wardend

mv $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json

wardend tendermint unsafe-reset-all --home $HOME/.warden
rm -fR $HOME/.warden/wasm

curl https://buenavista-genesis.s3.eu-west-1.amazonaws.com/warden-snaphot.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden

mv $HOME/.warden/priv_validator_state.json $HOME/.warden/data/priv_validator_state.json

sudo systemctl restart wardend && sudo journalctl -u wardend -f --no-hostname -o cat
```
