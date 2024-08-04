# Setup

**Install dependencies:**

Copy

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**Install GO: (amd64 - x86)**

Copy

```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.22.2.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

**Download binary Empeiria:**

Copy

```
cd $HOME
curl -LO https://github.com/empe-io/empe-chain-releases/raw/master/v0.1.0/emped_linux_amd64.tar.gz
tar -xvf emped_linux_amd64.tar.gz 
chmod +x emped
sudo mv emped /usr/local/bin
```

**Set chain and Name Empeiria:** _Change_ _\<Change-Name>_

Copy

```
emped init <Change-Name> --chain-id empe-testnet-2
```

**Custom Port: (Option)**

Copy

```
emped config node tcp://localhost:39657
sed -i -e "s%:1317%:3917%; s%:8080%:39580%; s%:9090%:39590%; s%:9091%:39591%; s%:8545%:3945%; s%:8546%:39546%; s%:6065%:39565%" $HOME/.symphonyd/config/app.toml
sed -i -e "s%:26658%:39658%; s%:26657%:39657%; s%:6060%:3960%; s%:26656%:39656%; s%:26660%:39661%" $HOME/.symphonyd/config/config.toml
```

**Set min gas:**

Copy

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = "0.001uempe"/" $HOME/.empe-chain/config/app.toml
```

**Set Pruning: (Option)**

Copy

```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.empe-chain/config/app.toml
```

**Set indexing: (Option)**

Copy

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.symphonyd/config/config.toml
```

**Download Genesis & addressbook:**

Copy

```
wget -O $HOME/.empe-chain/config/genesis.json https://node39.top/testnet/empeiria/genesis.json
wget -O $HOME/.empe-chain/config/addrbook.json https://node39.top/testnet/empeiria/addrbook.json
```

**Peers:**

Copy

```
peers="0b26ca19445d3c8bca20973fc1a4eca0f92e8b90@89.58.47.219:43656,2987c6802f3a227f2e423ec4548ae4f1a96cba9e@116.203.94.181:26656,d04a2a9a87f96a3749087859c0b069a1b6501763@89.117.63.63:28656,062ba91b7b59d3ea017ef08c7b500cac7f4ba13f@158.220.110.170:28656,e84288e6d6ea5c2903be3b051fb801accfac14c7@62.169.29.186:26656,e848b1559eec7374d5d6a331169d4a67d4f24233@5.78.88.250:36656,b07bd7db8557c50d326b1a8030bf7b224684cb31@195.179.229.217:49656,67201b187012b281eaca987b0a1691946c07c835@205.209.125.118:28656,b8d06304c2372ee83a83f1fc278aa3ddff9ed95e@65.21.232.175:28656,0b3443227967968f4e23ef43256ec84b2db9e273@212.192.222.90:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.symphonyd/config/config.toml
```

**Create Service:**

Copy

```
sudo tee /etc/systemd/system/emped.service > /dev/null <<EOF
[Unit]
Description=empe-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which emped) start --home $HOME/.empe-chain
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable emped
```

**Check sync: (False -> Done)**

Copy

```
emped status 2>&1 | jq .SyncInfo.catching_up
```

**Create Validator:**

Copy

```
emped tx staking create-validator \
--amount=1000000uempe \
--pubkey=$(emped tendermint show-validator) \
--moniker=Node39.TOP Guide \
--identity="xxxxxxxxx" \
--details="xxxxxxxxx" \
--website="xxxxxxxxx" \
--chain-id=empe-testnet-2 \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1000 \
--from=wallet \
--gas-adjustment=1.5 \
--gas="auto" \
--gas-prices=1uempe\ 
-y
```

**Edit Validator:**

Copy

```
emped tx staking edit-validator \
--new-moniker="Node39.TOP Guide" \
--identity="xxxxxxxxx" \
--details="xxxxxxxxx" \
--chain-id=empe-testnet-2 \
--commission-rate=0.1 \
--from=wallet \
--gas-prices=1uempe \
--gas-adjustment=1.5 \
--gas=auto \
-y
```
