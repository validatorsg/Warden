# Syncing

**Snapshort:**

Copy

```
sudo systemctl stop emped

mv $HOME/.empe-chain/data/priv_validator_state.json $HOME/.empe-chain/priv_validator_state.json.backup 

symphonyd tendermint unsafe-reset-all --home $HOME/.empe-chain --keep-addr-book 
curl https://node39.top/testnet/empeiria/snapshort-empeiria-193510.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.symphonyd

mv $HOME/.empe-chain/priv_validator_state.json.backup $HOME/.empe-chain/data/priv_validator_state.json 

sudo systemctl restart emped
sudo journalctl -u emped -f --no-hostname -o cat
```

**State sync:**

Copy

```
sudo systemctl stop emped

cp $HOME/.empe-chain/data/priv_validator_state.json $HOME/.empe-chain/priv_validator_state.json.backup
emped tendermint unsafe-reset-all --home $HOME/.empe-chain --keep-addr-book

SNAP_RPC="https://empeiria-testnet-rpc.node39.top:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.empe-chain/config/config.toml

mv $HOME/.empe-chain/priv_validator_state.json.backup $HOME/.empe-chain/data/priv_validator_state.json

sudo systemctl restart emped
sudo journalctl -u emped -f --no-hostname -o cat
```
