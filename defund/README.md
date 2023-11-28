### Install node
​
```bash
cd $HOME
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"

cd $HOME
wget -O go1.19.1.linux-amd64.tar.gz https://golang.org/dl/go1.19.1.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz && rm go1.19.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version

git clone https://github.com/defund-labs/defund
cd defund
git pull
git checkout 05390d047b3de40fbe23572e02ade4cd282a75ed
make install
sudo mv $HOME/go/bindefundd /usr/local/bin/defundd
cd $HOME

defundd init $Defund_NODENAME --chain-id defund-private-3
wget -O defund-private-3-gensis.tar.gz https://github.com/defund-labs/testnet/blob/main/defund-private-3/defund-private-3-gensis.tar.gz?raw=true
tar -zxvf $HOME/defund-private-3-gensis.tar.gz -C $HOME/.defund/config
rm -rf $HOME/defund-private-3-gensis.tar.gz

peers="d9184a3a61c56b803c7b317cd595e83bbae3925e@194.163.174.231:26677,5e7853ec4f74dba1d3ae721ff9f50926107efc38@65.108.6.45:60556,f114c02efc5aa7ee3ee6733d806a1fae2fbfb66b@65.108.46.123:56656,aa2c9df37e372c7928435075497fb0fb7ff9427e@38.129.16.18:26656,f2985029a48319330b99767d676412383e7061bf@194.163.155.84:36656,daff7b8cbcae4902c3c4542113ba521f968cc3f8@213.239.217.52:29656"
sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/;" $HOME/.defund/config/config.toml

pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.defund/config/app.toml
sed -i.bak -e "s/indexer *=.*/indexer = \"null\"/g" $HOME/.defund/config/config.toml

echo "[Unit]
Description=DeFund Node
After=network.target
​
[Service]
User=$USER
Type=simple
ExecStart=$(which defundd) start
Restart=on-failure
LimitNOFILE=65535
​
[Install]
WantedBy=multi-user.target" > $HOME/defund.service
sudo mv $HOME/defund.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF

sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable defund
sudo systemctl restart defund
sudo journalctl -u defund -f -o cat
```
