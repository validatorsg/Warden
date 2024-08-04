---
description: How to setup a new node for Warden Protocol
---

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
curl https://dl.google.com/go/go1.20.6.linux-arm64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

**Download Binary Warden Protocol:**

Copy

```
// AMD64

cd $HOME
rm -rf warden
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.3.2/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip && rm -rf wardend_Linux_x86_64.zip
chmod +x wardend
sudo mv wardend /usr/local/bin
wardend version

// ARM64

cd $HOME
rm -rf warden
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.3.2/wardend_Linux_arm64.zip
unzip wardend_Linux_arm64.zip && rm -rf wardend_Linux_arm64.zip
chmod +x wardend
sudo mv wardend /usr/local/bin
wardend version
```

**Set chain:** _Change_ _\<Change-Name>_

Copy

```
wardend init <Change-Name> --chain-id buenavista-1
```

**Set min gas:**

Copy

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0025uward"|g' $HOME/.warden/config/app.toml
```

**Set indexing: (Option)**

Copy

```
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.warden/config/config.toml
```

**Download Genesis & addressbook:**

Copy

```
wget -O $HOME/.warden/config/genesis.json https://node39.top/testnet/warden/genesis.json
wget -O $HOME/.warden/config/addrbook.json https://node39.top/testnet/warden/addrbook.json
```

**Peers:**

Copy

```
PEERS="fd6cf9438cfafe4a1fc35bb20456a856328febaa@37.27.47.29:39656,35c8779026ceb17659b722b6a768e5a7f070c770@84.247.161.158:31656,86fe149f801ac75213179be5b56fbd1a1e545c43@202.61.225.157:20656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.cardchaind/config/config.toml
```

**Create Service:**

Copy

```
sudo tee /etc/systemd/system/wardend.service > /dev/null <<EOF

[Unit]
Description=Warden Protocol
After=network-online.target

[Service]
User=root
ExecStart=$(which wardend) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable wardend
```

Wallet:

Copy

```
// Add New Wallet
wardend keys add wallet

// Restore executing wallet
wardend keys add wallet --recover

// List All Wallets
wardend keys list

// Delete wallet
wardend keys delete wallet

// Check Balance
wardend q bank balances $(wardend keys show wallet -a)

// Show validator
wardend tendermint show-validator

// Show EVM address
wardend keys unsafe-export-eth-key 

// Backup
Seed + priv_validator_key.json
```

**Check sync: (False -> Done)**

Copy

```
wardend status 2>&1 | jq .SyncInfo.catching_up
```
