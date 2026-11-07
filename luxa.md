---
description: >-
  Luxa LUXA Network is a sovereign Cosmos SDK Layer 1 secured by CometBFT
  proof-of-stake consensus. It supports native LUXA staking and governance,
  CosmWasm smart contracts, and IBC
---

# Luxa

### Links <a href="#links" id="links"></a>

* **Web**: [https://luxaecosystem.xyz/validator-guide](https://luxaecosystem.xyz/validator-guide)
* **TG**: [https://t.me/LuxaCommunity](https://t.me/LuxaCommunity)
* **Github**: [https://github.com/luxaecosystem/luxa-chain](https://github.com/luxaecosystem/luxa-chain)

### **Details** <a href="#details" id="details"></a>

* **Network Chain ID**: luxa-1
* **Binary**: luxad
* **Denom**: uluxa
* **Working directory**: .luxa

### **Public services** <a href="#details" id="details"></a>



* **Docs:** [https://luxaecosystem.xyz/validator-guide](https://luxaecosystem.xyz/validator-guide)
* **Explorer**: [https://explorer.validatorsg.com/Luxa-Mainnet](https://explorer.validatorsg.com/Luxa-Mainnet)
* **Guide**: https://services.validatorsg.com/luxa

### **Node installation** <a href="#details" id="details"></a>

<a class="button secondary">Copy</a>

```
git clone https://github.com/luxaecosystem/luxa-chain && cd luxa-chain
git checkout v1.0.0
make install
luxad version --long

# version: v1.0.0
# commit: ee7cb271c4770fa6435aec937ffe513a082998a7
```

**We initialize the node to create the necessary configuration files**

<a class="button secondary">Copy</a>

```
luxad init <your moniker> --chain-id luxa-1
```

**Download Genesis**

<a class="button secondary">Copy</a>

```
wget -O $HOME/.luxa/config/genesis.json "https://luxaecosystem.xyz/genesis.json"

sha256sum ~/.luxa/config/genesis.json
#3da9b57fa81220b9af6bd78f83956116579305d6474ea08b7756d70167199fa4
```

**At this stage, we can download the address book**

<a class="button secondary">Copy</a>

```
#wget -O $HOME/.luxa/config/addrbook.json "https://validatorsg.com/luxa/addrbook.json"
```

**Set up node configuration**

<a class="button secondary">Copy</a>

```
sed -i.bak -e "s/^chain-id *=.*/chain-id = \"luxa-1\"/;" ~/.luxa/config/client.toml
sed -i.bak -e "s/^keyring-backend *=.*/keyring-backend = \"os\"/;" ~/.luxa/config/client.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001uluxa\"/;" ~/.luxa/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.luxa/config/config.toml
peers="c6eb98f7a65a5fd23ef5c9004d792ec9a8e0b803@51.170.130.74:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.luxa/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.luxa/config/config.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.luxa/config/config.toml
```

**(OPTIONAL) Set up pruning**

<a class="button secondary">Copy</a>

```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="100"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.luxa/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.luxa/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.luxa/config/app.toml
```

**(OPTIONAL) Set up indexer**

<a class="button secondary">Copy</a>

```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.luxa/config/config.toml
```

**(OPTIONAL) Enable/Disable Snapshots**

<a class="button secondary">Copy</a>

```
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.luxa/config/app.toml
```

**Create a service file**

<a class="button secondary">Copy</a>

```
tee /etc/systemd/system/luxad.service > /dev/null <<EOF
[Unit]
Description=luxad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which luxad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

<a class="button secondary">Copy</a>

```
systemctl daemon-reload
systemctl enable luxad
systemctl restart luxad && journalctl -u luxad -f -o cat
```
