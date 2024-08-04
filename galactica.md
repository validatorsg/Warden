---
description: 'Hardware minimum:   2 cores  2 GB RAM  40 GB SSD  Ubuntu 22 - x86 or arm64'
---

# Galactica

Website: [https://galactica.com](https://galactica.com/)&#x20;

Telegram: [https://t.me/TheCypherState](https://t.me/TheCypherState)&#x20;

Discord: [http://discord.gg/galactica](http://discord.gg/galactica)

&#x20;X: [https://x.com/GalacticaNet](https://x.com/GalacticaNet)\
**Install Dependencies:**

Copy

```
sudo apt update && sudo apt upgarade -y
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

**Install GO: (amd64 - x86)**

Copy

```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.21.8.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

**Install GO: (arm64)**

Copy

```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.21.8.linux-arm64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

**Install Galactica:**

Copy

```
cd $HOME

rm -rf galactica

git clone https://github.com/Galactica-corp/galactica

cd galactica

git checkout v0.1.2

make install

galacticad version
```

**Set chain and Name Galactica:** _Change_ _\<Change-Name>_

Copy

```
galacticad init <Change-Name> --chain-id=galactica_9302-1
```

**Download Genesis & addressbook:**

Copy

```
curl -Ls https://node39.top/testnet/galaction/genesis.json > $HOME/.galactica/config/genesis.json 
curl -Ls https://node39.top/testnet/galactica/addrbook.json > $HOME/.galactica/config/addrbook.json
```

**Create Service:**

Copy

```
sudo tee /etc/systemd/system/galacticad.service > /dev/null <<EOF
[Unit]
Description=Galactica node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.galactica
ExecStart=$(which galacticad) start --home $HOME/.galactica --chain-id galactica_9302-1
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable galacticad
```

**Snapshort:** Every 4 hours

Copy

```
sudo systemctl stop galacticad

cp $HOME/.galactica/data/priv_validator_state.json $HOME/.galactica/priv_validator_state.json.backup

rm -rf $HOME/.galactica/data $HOME/.galactica/wasmPath
curl https://node39.top/testnet/galactica/node39-snapshort-galactica.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.galactica

mv $HOME/.galactica/priv_validator_state.json.backup $HOME/.galactica/data/priv_validator_state.json

sudo systemctl restart galacticad && sudo journalctl -u galacticad -f
```

**State sync:**

Copy

```
sudo systemctl stop galacticad

cp $HOME/.galactica/data/priv_validator_state.json $HOME/.galactica/priv_validator_state.json.backup
galacticad tendermint unsafe-reset-all --home $HOME/.galactica

peers="c9993c738bec6a10cfb8bb30ac4e9ae6f8286a5b@galactica-testnet-peer.itrocket.net:11656,3afb7974589e431293a370d10f4dcdb73fa96e9b@157.90.158.222:26656,6b846b316d704d78f3f9ca46d86cebc5a22de8ae@161.97.111.249:26656,c722e6dc5f762b0ef19be7f8cc8fd67cdf988946@49.12.96.14:26656,d572caf3a63d6c730fe0a5c586fd93e70683b727@167.86.127.19:656,e926f2e20568e61646558a2b8fd4a4e46d77903f@141.95.110.124:26656,7d8c640a24a1f15e98d45982bfd02dd0316c46e8@213.136.85.27:26656,f3cd6b6ebf8376e17e630266348672517aca006a@46.4.5.45:27456,dc4ed6e614725dffc41874e762a1b1ce4cdc3304@161.97.74.20:46656,e38c22e44893e75e388f3bcea2a075124d75ccd3@89.110.89.244:26656,8949fb771f2859248bf8b315b6f2934107f1cf5a@168.119.241.1:26656,9990ab130eac92a2ed1c3d668e9a1c6e811e8f35@148.251.177.108:27456"  
SNAP_RPC="https://galactica-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.galactica/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.galactica/config/config.toml

mv $HOME/.galactica/priv_validator_state.json.backup $HOME/.galactica/data/priv_validator_state.json

sudo systemctl restart galacticad && sudo journalctl -u galacticad -f
```

**Wallet:**

Copy

```
// Create wallet
galacticad keys add wallet

// Get Hex
galacticad keys convert-bech32-to-hex $(galacticad keys show wallet -a)

// Check Balance
galacticad q bank balances $(galacticad keys show wallet -a)
```

**Check sync: (False -> done)**

Copy

```
galacticad status 2>&1 | jq .SyncInfo.catching_up
```

**Create validator:**

Copy

```
galacticad tx staking create-validator \
--amount 9000000000000000000agnet \
--from wallet \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(galacticad tendermint show-validator) \
--moniker "xxx" \
--identity "xxx" \
--details "xxx" \
--website "xxx" \
--security-contact "email@xxx" \
--chain-id galactica_9302-1 \
--gas 200000 \
--gas-prices 10agnet \
-y
```

**Edit Validator:**

Copy

```
galacticad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "xxx" \
--identity "xxx" \
--details "xxx" \
--from wallet \
--chain-id galactica_9302-1 \
--gas 200000 \
--gas-prices 10agnet \
-y
```

**Delegate Token to your own validator:**

Copy

```
galacticad tx staking delegate $(galacticad keys show wallet --bech val -a) 1000000000000000000agnet --from wallet --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet  -y
```

**Withdraw rewards and commission from your validator:**

Copy

```
galacticad tx distribution withdraw-rewards $(galacticad keys show wallet --bech val -a) --from wallet --commission --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

**Unjail validator:**

Copy

```
galacticad tx slashing unjail --from wallet --chain-id galactica_9302-1 --gas 200000 --gas-prices 10agnet -y
```

**Command**:

Copy

```
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable galacticad

# Disable Service
sudo systemctl disable galacticad

# Start Service
sudo systemctl start galacticad

# Stop Service
sudo systemctl stop galacticad

# Restart Service
sudo systemctl restart galacticad

# Check Service Status
sudo systemctl status galacticad

# Check Service Logs
sudo journalctl -u galacticad -f --no-hostname -o cat
```

**More infomation:**

Copy

```
Website: https://galactica.com/

Twitter: https://twitter.com/GalacticaNet

Discord: https://discord.com/invite/galactica

Faucet: https://faucet-reticulum.galactica.com/
```
