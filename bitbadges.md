---
description: 'Network Type: Mainnet Chain-id: bitbadges-1 Current Node version: v12'
---

# BitBadges

#### Hardware requirements <a href="#hardware-requirements" id="hardware-requirements"></a>

The following requirements are recommended for running Defund:

* 4 or more physical CPU cores
* At least 200GB of SSD disk storage
* At least 8GB of memory (RAM)
* At least 100mbps network bandwidth

#### Install dependencies Required <a href="#install-dependencies-required" id="install-dependencies-required"></a>

```plaintext
sudo apt update && sudo apt upgrade -y && sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

#### Install go <a href="#install-go" id="install-go"></a>

We are gonna use GO Version 1.24.5 If you already have GO installed you can skip this

```plaintext
ver="1.24.5"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```

#### Install binary <a href="#install-binary" id="install-binary"></a>

```plaintext
cd $HOME
mkdir -p $HOME/.bitbadgeschain
wget https://github.com/BitBadges/bitbadgeschain/releases/download/v12/bitbadgeschain-linux-amd64 -O $HOME/go/bin/bitbadgeschaind
chmod +x $HOME/go/bin/bitbadgeschaind
```

#### Init `Change <MONIKER> with ur Name` <a href="#init-change-moniker-with-ur-name" id="init-change-moniker-with-ur-name"></a>

```plaintext
bitbadgeschaind init yourmoni--chain-id bitbadges-1
```

#### Download Genesis file and addrbook <a href="#download-genesis-file-and-addrbook" id="download-genesis-file-and-addrbook"></a>

* Genesis

```plaintext
curl -L https://snap.validatorsg.net/bitbadges/genesis.json > $HOME/.bitbadgeschain/config/genesis.json
```

* Addrbook

```plaintext
curl -L https://snap.validatorsg.net/bitbadges/addrbook.json > $HOME/.bitbadgeschain/config/addrbook.json
```

#### Custom Port <a href="#custom-port" id="custom-port"></a>

```plaintext
PORT=135
sed -i -e "s%:26657%:${PORT}57%" $HOME/.bitbadgeschain/config/client.toml
sed -i -e "s%:26658%:${PORT}58%; s%:26657%:${PORT}57%; s%:6060%:${PORT}60%; s%:26656%:${PORT}56%; s%:26660%:${PORT}61%" $HOME/.bitbadgeschain/config/config.toml
sed -i -e "s%:1317%:${PORT}17%; s%:9090%:${PORT}90%" $HOME/.bitbadgeschain/config/app.toml
```

#### Set Gas Prices <a href="#set-gas-prices" id="set-gas-prices"></a>

```plaintext
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ubadge\"/" $HOME/.bitbadgeschain/config/app.toml
```

#### Config pruning <a href="#config-pruning" id="config-pruning"></a>

```plaintext
sed -i \
-e 's|^pruning *=.*|pruning = "custom"|' \
-e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
-e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
-e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
$HOME/.bitbadgeschain/config/app.toml
```

#### Indexer Off <a href="#indexer-off" id="indexer-off"></a>

```plaintext
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.bitbadgeschain/config/config.toml
```

#### create service file and start node <a href="#create-service-file-and-start-node" id="create-service-file-and-start-node"></a>

```plaintext
sudo tee /etc/systemd/system/bitbadgeschaind.service > /dev/null <<EOF
[Unit]
Description=bitbadgeschain
After=network-online.target

[Service]
User=$USER
ExecStart=$(which bitbadgeschaind) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

* Enable Service and Start Node

```plaintext
sudo systemctl daemon-reload
sudo systemctl enable bitbadgeschaind
sudo systemctl restart bitbadgeschaind
sudo journalctl -u bitbadgeschaind -f -o cat
```
