#!/bin/bash

echo "www.skynodejs.net"
echo "github/0xMrmoon"
echo "Ty for the original code kjnodes & nodeist "
echo "kjnodes.com"
echo "nodeist.net"

sleep 2

# Variables Skynodes
HMN_WALLET=wallet
HMN=humansd
HMN_ID=testnet-1
HMN_PORT=55
HMN_FOLDER=.humans
HMN_FOLDER2=humans
HMN_VER=v1.0.0
HMN_REPO=https://github.com/humansdotai/humans
HMN_GENESIS=https://snapshots.polkachu.com/testnet-genesis/humans/genesis.json
HMN_ADDRBOOK=
HMN_MIN_GAS=0
HMN_DENOM=uheart
HMN_SEEDS=
HMN_PEERS=1df6735ac39c8f07ae5db31923a0d38ec6d1372b@45.136.40.6:26656,9726b7ba17ee87006055a9b7a45293bfd7b7f0fc@45.136.40.16:26656,6e84cde074d4af8a9df59d125db3bf8d6722a787@45.136.40.18:26656,eda3e2255f3c88f97673d61d6f37b243de34e9d9@45.136.40.13:26656,4de8c8acccecc8e0bed4a218c2ef235ab68b5cf2@45.136.40.12:26656


sleep 1

echo "export HMN_WALLET=${HMN_WALLET}" >> $HOME/.bash_profile
echo "export HMN=${HMN}" >> $HOME/.bash_profile
echo "export HMN_ID=${HMN_ID}" >> $HOME/.bash_profile
echo "export HMN_PORT=${HMN_PORT}" >> $HOME/.bash_profile
echo "export HMN_FOLDER=${HMN_FOLDER}" >> $HOME/.bash_profile
echo "export HMN_FOLDER2=${HMN_FOLDER2}" >> $HOME/.bash_profile
echo "export HMN_VER=${HMN_VER}" >> $HOME/.bash_profile
echo "export HMN_REPO=${HMN_REPO}" >> $HOME/.bash_profile
echo "export HMN_GENESIS=${HMN_GENESIS}" >> $HOME/.bash_profile
echo "export HMN_PEERS=${HMN_PEERS}" >> $HOME/.bash_profile
echo "export HMN_SEED=${HMN_SEED}" >> $HOME/.bash_profile
echo "export HMN_MIN_GAS=${HMN_MIN_GAS}" >> $HOME/.bash_profile
echo "export HMN_DENOM=${HMN_DENOM}" >> $HOME/.bash_profile
source $HOME/.bash_profile

sleep 1

if [ ! $HMN_NODENAME ]; then
	read -p "Your node name: " HMN_NODENAME
	echo 'export HMN_NODENAME='$HMN_NODENAME >> $HOME/.bash_profile
fi

echo -e "NODE NAME: \e[1m\e[32m$HMN_NODENAME\e[0m"
echo -e "WALLET NAME: \e[1m\e[32m$HMN_WALLET\e[0m"
echo -e "CHAIN : \e[1m\e[32m$HMN_ID\e[0m"
echo -e "PORT : \e[1m\e[32m$HMN_PORT\e[0m"
echo '================================================='

sleep 2


# UPGRADES by skynodes
echo -e "\e[1m\e[32m1. upgrades... \e[0m" && sleep 1
sudo apt update && sudo apt upgrade -y


# Install.Packages 
echo -e "\e[1m\e[32m2. Install.Packages(liblz4-tool)... \e[0m" && sleep 1
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

# GO skynodes
echo -e "\e[1m\e[32m1. GO ... \e[0m" && sleep 1
ver="1.18.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version

sleep 1

# Library by Skynodes
echo -e "\e[1m\e[32m1. REPO ... \e[0m" && sleep 1
cd $HOME
git clone $HMN_REPO
cd $HMN_FOLDER2
git checkout $HMN_VER
go build -o humansd cmd/humansd/main.go
sudo cp humansd /usr/local/bin/humansd

sleep 1

# 
echo -e "\e[1m\e[32m1. ... \e[0m" && sleep 1
$HMN config chain-id $HMN_ID
$HMN config keyring-backend file
$HMN init $HMN_NODENAME --chain-id $HMN_ID

# ADDRBOOK 
wget $HMN_GENESIS -O $HOME/$HMN_FOLDER/config/genesis.json
wget $HMN_ADDRBOOK -O $HOME/$HMN_FOLDER/config/addrbook.json

# 
SEEDS="$HMN_SEEDS"
PEERS="$HMN_PEERS"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/$HMN_FOLDER/config/config.toml

sleep 1


# 
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/$HMN_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/$HMN_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/$HMN_FOLDER/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/$HMN_FOLDER/config/app.toml


# 
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:2${HMN_PORT}8\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:2${HMN_PORT}7\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${HMN_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:2${HMN_PORT}6\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":2${HMN_PORT}0\"%" $HOME/$HMN_FOLDER/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${HMN_PORT}7\"%; s%^address = \":8080\"%address = \":${HMN_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${HMN_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${HMN_PORT}91\"%" $HOME/$HMN_FOLDER/config/app.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:2${HMN_PORT}7\"%" $HOME/$HMN_FOLDER/config/client.toml

# PROMETHEUS 
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/$HMN_FOLDER/config/config.toml

# MINIMUM 
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00125$HMN_DENOM\"/" $HOME/$HMN_FOLDER/config/app.toml

# INDEXER 
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/$HMN_FOLDER/config/config.toml

# RESET 
$HMN tendermint unsafe-reset-all --home $HOME/$HMN_FOLDER

# 
echo -e "\e[1m\e[32m4... \e[0m" && sleep 1
sudo tee /etc/systemd/system/$HMN.service > /dev/null <<EOF
[Unit]
Description=$HMN
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which $HMN) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF


#
sudo systemctl daemon-reload
sudo systemctl enable $HMN
sudo systemctl restart $HMN

echo '=============== skynodes ==================='
echo -e 'check your logs: \e[1m\e[32mjournalctl -fu humansd -o cat \e[0m'
echo -e "check your sync: \e[1m\e[32mcurl -s localhost:${HMN_PORT}657/status | jq .result.sync_info\e[0m"

source $HOME/.bash_profile
