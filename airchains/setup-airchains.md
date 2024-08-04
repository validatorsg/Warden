---
description: >-
  Welcome to the new version of Junction, a cutting-edge blockchain platform
  designed for secure and efficient data management.
---

# Airchains

**Recommended Hardware**: 4 Core, 8Gb ram, 200Gb SSD NVMe **Minumum Hardware**: 2 Core, 4Gb ram, 40Gb SSD NVMe **Ubuntu 22.04, x86 or arm**

Faucet: Discord

```
Website: https://www.airchains.io/
Discord: https://discord.gg/airchains
X: https://twitter.com/airchains_io
```

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

**Install Airchains: amd64**

Copy

```
cd $HOME
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin/
cd $HOME
junctiond version
```

**Install Airchains: arm64 (Node39 build from project source)**

Copy

```
cd $HOME
wget https://node39.top/testnet/airchains/junctiond-linux-arm64
mv junctiond-linux-arm64 junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin/
```

**Set chain and Name Airchains:** _Change_ _\<Change-Name>_

Copy

```
junctiond init <Change-Name> --chain-id junction
```

**Download Genesis & addressbook:**

Copy

```
curl -Ls https://node39.top/testnet/airchains/genesis.json > $HOME/.junction/config/genesis.json 
curl -Ls https://node39.top/testnet/airchains/addrbook.json > $HOME/.junction/config/addrbook.json
```

**Peers:**

Copy

```
peers="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,1918bd71bc764c71456d10483f754884223959a5@35.240.206.208:26656,48887cbb310bb854d7f9da8d5687cbfca02b9968@35.200.245.190:26656,de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,8b72b2f2e027f8a736e36b2350f6897a5e9bfeaa@131.153.232.69:26656,d92c7efcb453ba2edab6d80ad6e3b692e3a7e4f5@49.13.120.225:26656,5c5989b5dee8cff0b379c4f7273eac3091c3137b@57.128.74.22:56256,e09fa8cc6b06b99d07560b6c33443023e6a3b9c6@65.21.131.187:26656,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,3e5f3247d41d2c3ceeef0987f836e9b29068a3e9@168.119.31.198:56256,086d19f4d7542666c8b0cac703f78d4a8d4ec528@135.148.232.105:26656,976a0fe0a0fa205478beb66addaae3842907c3f6@37.27.48.77:32656,7d6694fb464a9c9761992f695e6ba1d334403986@164.90.228.66:26656,b2e9bebc16bc35e16573269beba67ffea5932e13@95.111.239.250:26656,23152e91e3bd642bef6508c8d6bd1dbedccf9e56@95.111.237.24:26656,c1e9d12d80ec74b8ddbabdec9e0dad71337ba43f@135.181.82.176:26656,3b429f2c994fa76f9443e517fd8b72dcf60e6590@37.27.11.132:26656,84b6ccf69680c9459b3b78ca4ba80313fa9b315a@159.69.208.30:26656,e78a440c57576f3743e6aa9db00438462980927e@5.161.199.115:26656,49fb1316b22c71e455720af15dd552dafb9af39a@5.189.151.175:26656,e831fa909cce0d1807cfcf417e28e782530f5c94@161.97.66.254:26666,db38d672f66df4de01b26e1fa97e1632fbfb1bdf@173.249.57.190:26656,08a0014125bded5fe76b9dc3275b0a58b6841b43@116.203.184.36:26656,6025c1523ad0cd6926ef277cfcf46d82ebb43c21@116.203.24.46:26656,fed2e80e159a23bf9f71f980b501c2304cec2f6d@185.194.216.61:17656,1ad9bdeac0b06f585a9c64261d0705c4cbfd28e7@144.91.99.93:26656,a6384bd23bd728ffa9a8452b12fc865dadf51672@81.200.154.160:26656,5b31fdf605645b44ad615c8b79b1550540895fe5@35.214.147.230:26656,6a3a13d7631823eb6dcd00882243c913c819a125@38.242.196.100:26656,3e182e463425dfa6d4cef83f4bdd67c98c36eba3@195.26.243.208:47656,c97c7a9c11cf3cb059ce89c36f7ff219daa3ada4@195.26.246.26:26656,7d162ef2392d25720d7cdb2cfdf2ccf146e32bac@49.13.234.149:26656,5a161464aa73571f1b7e22204bcf3bdb6fb71f3b@195.26.241.184:26656,bff7c802021ed3b4aaf222da9d42280bfc5dad88@65.109.139.181:26656,c284cbda3ab197001136c39c9df8e45af2038513@34.93.143.222:26656,449297568d9d6d4aa51a93f7a1b1e92e1ec38619@65.108.242.9:26656,611440c7193678ec1cd0c60b55abfca07dfa27cd@95.217.161.97:26656,beec52199d4b28cab6fc3b2f2a2718c6667ac46a@37.27.19.95:26656,226b9c42e81cddd185d435348cd89f87fee37279@135.181.42.46:26656,479b63df84e247c55e80cccd9abbf7100a334fcc@65.108.156.83:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.junction/config/config.toml
```

**Create Service:**

Copy

```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=junctiond
After=network-online.target
[Service]
User=root
ExecStart=$(which junctiond) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable junctiond
```

**Set min gas:**

Copy

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.001amf"|g' $HOME/.junction/config/app.toml
```

**Download Snapshort:**

Copy

```
sudo systemctl stop junctiond
cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
curl https://node39.top/testnet/airchains/snapshort-airchains.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction
mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f --no-hostname -o cat
```

**Wallet:**

Copy

```
// Create wallet
junctiond keys add wallet

// Recover wallet
junctiond keys add wallet --recover

// Check Balance
junctiond q bank balances $(junctiond keys show wallet -a)
```

**Check sync: (False -> done)**

Copy

```
junctiond status 2>&1 | jq .sync_info
```

**Create validator:**

Copy

```

cd $HOME

// Check validator public key
junctiond comet show-validator

Ex: {"@type":"/cosmos.crypto.ed25519.PubKey","key":"sElDJvnNcALvCAirlerzK9tzsf5CXPmDPVz0Bvm9EjQ="}

// Create & edit file json
nano validator.json

{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"sElDJvnNcALvCAirlerzK9tzsf5CXPmDPVz0Bvm9EjQ="},
	"amount": "1000000amf",
	"moniker": "<change-name>",
	"identity": "",
	"website": "",
	"security": "",
	"details": "",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}

// Create validator
junctiond tx staking create-validator validator.json --from wallet --chain-id junction --fees 5000amf -y
```

**Delegate Token to your own validator:**

Copy

```
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a)  1000000amf --from wallet --chain-id junction --fees 5000amf -y
```

**Withdraw rewards and commission from your validator:**

Copy

```
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --commission --from wallet --chain-id junction --fees 5000amf -y
```

**Unjail validator:**

Copy

```
junctiond tx slashing unjail --from wallet --chain-id junction --fees 5000amf -y
```

Copy

```
// Reload Service
sudo systemctl daemon-reload

// Enable Service
sudo systemctl enable junctiond

// Disable Service
sudo systemctl disable junctiond

// Start Service
sudo systemctl start junctiond

// Stop Service
sudo systemctl stop junctiond

// Restart Service
sudo systemctl restart junctiond

// Check Service Status
sudo systemctl status junctiond

// Check Service Logs
sudo journalctl -u junctiond -f --no-hostname -o cat
```

**Detele node:**

Copy

```
sudo systemctl stop junctiond
sudo systemctl disable junctiond
sudo rm -rf /etc/systemd/system/junctiond.service
sudo rm $(which junctiond)
sudo rm -rf $HOME/.junction
sed -i "/AIRCHAIN_/d" $HOME/.bash_profile
```
