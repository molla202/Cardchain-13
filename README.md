


![image](https://github.com/molla202/Cardchain-Testnet-6/assets/91562185/42753ec7-d7e2-4ae2-9f91-b58d3e50491c)












 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [CrowdControl Website](https://crowdcontrol.network/)<br>
 * [Blockchain Explorer](https://testnet.itrocket.net/cardchain/staking)<br>
 * [Discord](https://discord.gg/yYhRhK88SP)<br>
 * [CrowdControl Twitter](https://twitter.com/CrowdControlNet)<br>

## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4|
| RAM	| 8+ GB |
| Storage	| 400 GB SSD |


### 🚧Update ve gereklilikler
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```



### 🚧 Go 
```
cd $HOME
GO_VERSION="1.22.0"
wget "https://golang.org/dl/go${GO_VERSION}.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go${GO_VERSION}.linux-amd64.tar.gz"
rm "go${GO_VERSION}.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```
### 🚧 Varyasyonları ayarlıyalım
```
Not: Aşağıdaki wallet ve moniker kısmına kendi adınızı giriniz...
echo "export WALLET="molla202"" >> $HOME/.bash_profile
echo "export MONIKER="molla202"" >> $HOME/.bash_profile
echo "export CARDCHAIN_CHAIN_ID="cardtestnet-13"" >> $HOME/.bash_profile
echo "export CARDCHAIN_PORT="31"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
### 🚧 Dosyaları çekip kuruyoruz
```
cd $HOME

rm -rf Cardchain

git clone https://github.com/DecentralCardGame/Cardchain.git

cd Cardchain

git checkout v0.17.0

make install
```
### 🚧 Configurasyonlar
```
Cardchaind config node tcp://localhost:${CARDCHAIN_PORT}657
Cardchaind config keyring-backend os
Cardchaind config chain-id cardtestnet-13
Cardchaind init "$MONIKER" --chain-id cardtestnet-13
```
### 🚧 Genesis ve adressbook
```
wget -O $HOME/.Cardchain/config/genesis.json https://cardchain.crowdcontrol.network/files/genesis.json

```
### 🚧 Seed ve Peer
```
SEEDS=""
PEERS="77dbc0da2b3f1d066676b316aa9bd97f4926b1ac@152.53.103.89:32056"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.Cardchain/config/config.toml
```
### 🚧 port
```
sed -i.bak -e "s%:1317%:${CARDCHAIN_PORT}317%g;
s%:8080%:${CARDCHAIN_PORT}080%g;
s%:9090%:${CARDCHAIN_PORT}090%g;
s%:9091%:${CARDCHAIN_PORT}091%g;
s%:8545%:${CARDCHAIN_PORT}545%g;
s%:8546%:${CARDCHAIN_PORT}546%g;
s%:6065%:${CARDCHAIN_PORT}065%g" $HOME/.Cardchain/config/app.toml
```
### 🚧 port
```
sed -i.bak -e "s%:26658%:${CARDCHAIN_PORT}658%g;
s%:26657%:${CARDCHAIN_PORT}657%g;
s%:6060%:${CARDCHAIN_PORT}060%g;
s%:26656%:${CARDCHAIN_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${CARDCHAIN_PORT}656\"%;
s%:26660%:${CARDCHAIN_PORT}660%g" $HOME/.Cardchain/config/config.toml
```
### 🚧 Puring
```
sed -i -e "s/^pruning *=.*/pruning = \"nothing\"/" $HOME/.Cardchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.Cardchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.Cardchain/config/app.toml
```
### 🚧 Gas ve diğer ayarlar
```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0ubpf"|g' $HOME/.Cardchain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.Cardchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.Cardchain/config/config.toml
```
### 🚧 Servis Dosyası
```
sudo tee /etc/systemd/system/Cardchaind.service > /dev/null <<EOF
[Unit]
Description=Cardchain node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.Cardchain
ExecStart=$(which Cardchaind) start --home $HOME/.Cardchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
### 🚧 Snap
```

```
### 🚧 Servisleri başlatalım
```
sudo systemctl daemon-reload
sudo systemctl enable Cardchaind
sudo systemctl restart Cardchaind && sudo journalctl -u Cardchaind -fo cat
```

### Cüzdan oluşturma yada import
```
Cardchaind keys add cüzdan-adı
```
OR import
```
Cardchaind keys add cüzdan-adı --recover
```

### Validator olusturma
```
Cardchaind tx staking create-validator \
--amount 1000000ubpf \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(Cardchaind tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "" \
--chain-id cardtestnet-13 \
--gas auto --gas-adjustment 1.5 \
-y
```
