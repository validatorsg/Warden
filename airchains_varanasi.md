# Airchains\_Varanasi

## install go, if needed

```bash
cd $HOME
VER="1.21.6"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

## set vars

echo "export WALLET="wallet"" >> $HOME/.bash\_profile\
echo "export MONIKER="test"" >> $HOME/.bash\_profile\
echo "export AIRCHAIN\_CHAIN\_ID="varanasi-1"" >> $HOME/.bash\_profile\
echo "export AIRCHAIN\_PORT="19"" >> $HOME/.bash\_profile\
source $HOME/.bash\_profile

## download binary

cd $HOME\
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.3.1/junctiond-linux-amd64\
chmod +x junctiond\
mv junctiond $HOME/go/bin/

## config and init app

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y\
source $HOME/.cargo/env\
junctiond init $MONIKER --chain-id $AIRCHAIN\_CHAIN\_ID\
sed -i -e "s|^node _=._|node = "tcp://localhost:${AIRCHAIN\_PORT}657"|" $HOME/.junctiond/config/client.toml

## download genesis and addrbook

wget -O $HOME/.junctiond/config/genesis.json https://server-7.itrocket.net/testnet/airchains\_v/genesis.json\
wget -O $HOME/.junctiond/config/addrbook.json https://server-7.itrocket.net/testnet/airchains\_v/addrbook.json

## set seeds and peers

SEEDS="97cadd453fa35cee05f72611fdb15a49112575cb@airchains\_v-testnet-seed.itrocket.net:19656"\
PEERS="79f26210777e84efb600bf776c32615a72675d9f@airchains\_v-testnet-peer.itrocket.net:19656,4eff6ecc2323811d18c7e06319b2d8bbf58590d1@65.108.233.73:19656,562e107ee9ea7155b1b277a823f563e4f070d572@\[2a03:cfc0:8000:13::b910:27be]:13756,f84b41b95e828ee915aea19dd656cca7d39cf47b@37.17.244.207:33656,43c265128fd9be02721df03e8ba4bcf8c982a062@1.53.252.54:26656,ad40b693a907181cad7f9db73ae7590206418d5e@65.109.84.33:26756,1d7a1809b616ce2437a5978bebbfcefec4bc3aa0@193.34.212.80:60656,b43f7c96bb780d9ac535d3c1f78092cf8c455e85@104.36.23.246:26656,d55b5d55e89d22c2b8dcaeb09b001b3b068af91d@37.27.127.145:63656,0b4e78189c9148dda5b1b98c6e46b764337558a3@91.227.33.18:19656"\
sed -i -e "/^\[p2p]/,/^\[/{s/^\[\[:space:]]\*seeds _=._/seeds = "$SEEDS"/}"\
-e "/^\[p2p]/,/^\[/{s/^\[\[:space:]]\*persistent\_peers _=._/persistent\_peers = "$PEERS"/}" $HOME/.junctiond/config/config.toml

## set custom ports in app.toml

sed -i.bak -e "s%:1317%:${AIRCHAIN\_PORT}317%g;\
s%:8080%:${AIRCHAIN\_PORT}080%g;\
s%:9090%:${AIRCHAIN\_PORT}090%g;\
s%:9091%:${AIRCHAIN\_PORT}091%g;\
s%:8545%:${AIRCHAIN\_PORT}545%g;\
s%:8546%:${AIRCHAIN\_PORT}546%g;\
s%:6065%:${AIRCHAIN\_PORT}065%g" $HOME/.junctiond/config/app.toml

## set custom ports in config.toml file

sed -i.bak -e "s%:26658%:${AIRCHAIN\_PORT}658%g;\
s%:26657%:${AIRCHAIN\_PORT}657%g;\
s%:6060%:${AIRCHAIN\_PORT}060%g;\
s%:26656%:${AIRCHAIN\_PORT}656%g;\
s%^external\_address = ""%external\_address = "$(wget -qO- eth0.me):${AIRCHAIN\_PORT}656"%;\
s%:26660%:${AIRCHAIN\_PORT}660%g" $HOME/.junctiond/config/config.toml

## config pruning

sed -i -e "s/^pruning _=._/pruning = "custom"/" $HOME/.junctiond/config/app.toml\
sed -i -e "s/^pruning-keep-recent _=._/pruning-keep-recent = "100"/" $HOME/.junctiond/config/app.toml\
sed -i -e "s/^pruning-interval _=._/pruning-interval = "19"/" $HOME/.junctiond/config/app.toml

## set minimum gas price, enable prometheus and disable indexing

sed -i 's|minimum-gas-prices =.\*|minimum-gas-prices = "0.001amf"|g' $HOME/.junctiond/config/app.toml\
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.junctiond/config/config.toml\
sed -i -e "s/^indexer _=._/indexer = "null"/" $HOME/.junctiond/config/config.toml

## create service file

sudo tee /etc/systemd/system/junctiond.service > /dev/null <\<EOF\
\[Unit]\
Description=Airchains\_v node\
After=network-online.target\
\[Service]\
User=$USER\
WorkingDirectory=$HOME/.junctiond\
ExecStart=$(which junctiond) start --home $HOME/.junctiond\
Restart=on-failure\
RestartSec=5\
LimitNOFILE=65535\
\[Install]\
WantedBy=multi-user.target\
EOF

## reset and download snapshot

junctiond tendermint unsafe-reset-all --home $HOME/.junctiond\
if curl -s --head curl https://server-7.validatorsg.net/testnet/airchains\_v/airchains\_v\_2025-03-23\_16086\_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then\
curl https://server-7.validatorsg.net/testnet/airchains\_v/airchains\_v\_2025-03-23\_16086\_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junctiond\
else\
echo "no snapshot found"\
fi

## enable and start service

sudo systemctl daemon-reload\
sudo systemctl enable junctiond\
sudo systemctl restart junctiond && sudo journalctl -u junctiond -fo cat
