---
description: 'Web: https://tellor.io/'
---

# Tellor

### Server preparation <a href="#server-preparation" id="server-preparation"></a>

Copy

```
apt update && apt upgrade -y
```

Copy

```
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

**Install GO**

Copy

```
ver="1.23.3"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

### Node installation <a href="#node-installation" id="node-installation"></a>

Copy

```
git clone https://github.com/tellor-io/layer && cd layer
#git checkout v4.0.0
#make install

cd $HOME/layer
wget https://github.com/tellor-io/layer/releases/download/v5.1.2/layer_Linux_x86_64.tar.gz
tar -xvzf layer_Linux_x86_64.tar.gz
mv $HOME/layer/layerd $(which layerd)

layerd version --long | grep -e version -e commit
# version: 5.1.2
# commit: 683b709191e1342b8722f62b0f9bb897b525a92f
```

**We initialize the node to create the necessary configuration files**

Copy

```
layerd init validatorsg_guide --chain-id layertest-5
```

**Download Genesis**

Copy

```
curl https://node-palmito.tellorlayer.com/rpc/genesis | jq '.result.genesis' > ~/.layer/config/genesis.json

sha256sum ~/.layer/config/genesis.json
# b65ea5530e3ab666005b67103245b0f07603660c67656b7a6a057ada10212885
```

**At this stage, we can download the address book**

Copy

```
wget -O $HOME/.layer/config/addrbook.json "https://share102.utsa.tech/tellor/addrbook.json"
```

**Set up node configuration**

Copy

```
sed -i.bak -e "s/^chain-id *=.*/chain-id = \"layertest-4\"/;" ~/.layer/config/client.toml
sed -i.bak -e "s/^keyring-backend *=.*/keyring-backend = \"test\"/;" ~/.layer/config/client.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.000025loya\"/;" ~/.layer/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.layer/config/config.toml

peers="c7b175a5bafb35176cdcba3027e764a0dbd0811c@34.219.95.82:26656,05105e8bb28e8c5ace1cecacefb8d4efb0338ec6@18.218.114.74:26656,705f6154c6c6aeb0ba36c8b53639a5daa1b186f6@3.80.39.230:26656,1f6522a346209ee99ecb4d3e897d9d97633ae146@3.101.138.30:26656,3822fa2eb0052b36360a7a6e285c18cc92e26215@175.41.188.192:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.layer/config/config.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.layer/config/config.toml
sed -i 's/timeout_commit = "5s"/timeout_commit = "1s"/' ~/.layer/config/config.toml
```

**(OPTIONAL) Set up pruning**

Copy

```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.layer/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.layer/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.layer/config/app.toml
```

**(OPTIONAL) Set up indexer**

Copy

```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.layer/config/config.toml
```

**(OPTIONAL) Enable/Disable Snapshots**

Copy

```
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.layer/config/app.toml
```

**Create a service file**

Copy

```
tee /etc/systemd/system/layerd.service > /dev/null <<EOF
[Unit]
Description=layerd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which layerd) start --home /root/.layer --key-name <name_wallet> --keyring-backend test
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Copy

```
systemctl daemon-reload
systemctl enable layerd
systemctl restart layerd && journalctl -u layerd -f -o cat
```
