# Встановлення ноди Nibiru

Nibiru- це суверенний proof-of-stake блокчейн, платформа з відкритим вихідним кодом і член сімейства взаємопов'язаних блокчейнів, що складають екосистему Cosmos.

Nibiru об'єднує торгівлю деривативами з кредитним плечем, спотову торгівлю, стейкінг і надання облігаційної ліквідності в єдиний призначений для користувача інтерфейс, даючи змогу користувачам більш ніж 40 блокчейнів торгувати з кредитним плечем, використовуючи набір децентралізованих застосунків, які компонують, більш докладно можна прочитати на офіційному сайті.

### Рекомендовані вимоги для сервера

## 4GB RAM 100GB+ of disk space 2 Cores (modern CPU's)


### Оновлюємо наш сервер
```
sudo apt update && sudo apt upgrade -y
```

### Встановлюємо додаткові пакети
```
sudo apt install make clang pkg-config libssl-dev build-essential git gcc chrony curl jq ncdu bsdmainutils htop net-tools lsof fail2ban wget -y
```

### Встановлюємо go і перевіряємо версію
```
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

### Після цього завантажуємо і встановлюємо бінарник
```
cd $HOME
git clone https://github.com/NibiruChain/nibiru.git
cd nibiru
git checkout v0.16.2
make build
```

### Переміщуємо бінарник у робочий каталог
```
sudo mv ./build/nibid /usr/local/bin/
cd $HOME
```

### Задаємо змінні (CHAIN залишаємо без змін, в інші вписуємо свої дані)
```
NIBIRU_MONIKER="your_name"
NIBIRU_CHAIN="nibiru-testnet-2"
NIBIRU_WALLET="your_name"
```

### Додаємо все в баш профіль
```
echo 'export NIBIRU_MONIKER='${NIBIRU_MONIKER} >> $HOME/.bash_profile
echo 'export NIBIRU_CHAIN='${NIBIRU_CHAIN} >> $HOME/.bash_profile
echo 'export NIBIRU_WALLET='${NIBIRU_WALLET} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Ініціалізуємо ноду
```
nibid init $NIBIRU_MONIKER --chain-id $NIBIRU_CHAIN
```

### Прописуємо в конфіг ім'я мережі
```
nibid config chain-id $NIBIRU_CHAIN
```

### Завантажуємо файл генезис
```
curl -s https://rpc.testnet-2.nibiru.fi/genesis | jq -r .result.genesis > $HOME/.nibid/config/genesis.json
```

### Налаштовуємо прунінг (за бажанням)
```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.nibid/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.nibid/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.nibid/config/app.toml
```

### Вимикаємо індексер (за бажанням)
```
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.nibid/config/config.toml
```

### Додаємо сіди
```
sed -i 's|seeds =.*|seeds = "'$(curl -s https://networks.testnet.nibiru.fi/nibiru-testnet-2/seeds)'"|g' $HOME/.nibid/config/config.toml
```

### Додаємо піри
```
peers="7f8bd4eaf6b9b213fd7b89ceefc517bcaa517d24@5.9.147.22:22656,8ff8d3effc84c1e5d7bdff36d8921875f7436bcd@65.108.13.185:26858,38d128d24e7d9cbdd80227004a7ca0fa129109b5@65.109.92.148:60656,a4201d74ccf67e93939c361d981745fb8d5a05aa@84.46.241.140:26656,5ef59d8905bbd2bff62e06c391bfcccd5b4f23a9@188.34.202.151:26656,88962b5c14cadacdde0ecd1fa5f941071686d287@103.179.189.236:26656,1bda79b6e077da99ba3f1cde34d85bcd36c25de2@5.78.51.159:26656,062c91e5ce1fd67659ea2b442cea79cd648cf10a@51.91.144.243:26656,c08c4d5060697a644838403329be5742bdb4c67f@65.109.92.240:11036,93137cb574b5d6bd6fdb60e6c8164a08c1516081@209.126.8.192:26656,b32bb87364a52df3efcbe9eacc178c96b35c823a@135.181.115.111:27656,c72e69f79dddf63d5c5d8fda2777d313500e8264@82.208.22.68:26656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:39656,283477551bc231c6c473f581e4b34deb82741db8@185.135.137.215:26656,528ac8cfeefadd4d66d87118ae0dae4db1d1d14e@173.249.14.243:26656,203f5233ce57c52df3d74f038007065c8b4ddf9e@45.10.154.247:26656,5b38a5b453dd532b280aeb6ad05383ea4e22171f@138.197.183.235:26656,a2d82d25353376b74df2985f3442029470c1e1e7@5.161.148.133:26656,2994d6169f362952fabe420922f70293d77a7567@38.242.159.204:26656,a65dd1e2e7f4989dc85465812705fb2b844cc43d@185.208.206.205:26656,21e949ee5e19df867434fa145e26c8982e325c2a@185.211.6.44:26656,6a08b01e52b0fd256a547a0d5e8a0e73edf7a75d@45.10.154.207:26656,05e9375c54a4c6a4918b7e2bf5de272c6303504c@2.58.82.216:26656,858ddaf58e566918591802ba04ce3647c5b01707@65.109.106.91:15656,71cdf4bdd1852dfaf3f4aa1a8295ade52d7c426d@185.135.137.237:26656,bcceb73119b08bdaf83121f11a00121cbcbbfe59@185.135.137.244:26656,043145ef5f5e3cdb1132bfc1c6a562800f66365f@45.10.154.248:26656,756a7ac7c297a6b0c5015501ad7ad484867c8c96@213.246.39.53:26656,12713bf935ea2f1f74aa2e19bba2fa2f27d69c7e@194.60.201.124:26656,f0fc550ade0f29b8ae91e9a8b45aec0b9fd81c7e@185.135.137.211:26656,7dca3f7803f04a9999ae546ff3fcedbbc6a0aa18@185.135.137.239:26656,703c812803e8c2bb13c6fcc716ab9d63c3f34152@2.58.82.50:26656,794f2f7e5bb4e9b1e7e752c3d7df76a8db824151@65.109.30.12:61756,144708f067d0f081bc012051a75ca91f6526a8c9@185.216.203.37:26656,3b53fcc0bf347f1c93d15edd7f5d51cf44ee311f@185.135.137.228:26656,719e5c2c79f027c65514d70e0f08d754119a6f0c@45.10.154.246:26656,fb5b3e6f22e392a30712b68609019440361571f4@185.135.137.217:26656,382ae7d147e493a52b9a3bc9bff5fa4d09f9e13c@141.95.102.233:39656,e4a1cd0823408fe4af04759ead69320757c409cb@109.123.244.118:26656,fe95705d3de436dcef390c5ed7cd44d500c32738@185.135.137.254:26656,cb6c12e6e457b824d236b82e7c7547a9ed10f95a@213.137.237.201:26656,097262b931c9493d47ee73ceb408989dff8cb626@45.10.154.160:26656,f25fe90abaa1185200e2430404951bd5d17ebbde@185.213.27.177:26656,17c2fd516be2fdebb5f291b3bf70be6439e96ae3@194.60.201.185:26656,9a7689b2ab4d210c3cbc4af27589073823347e50@176.126.87.201:26656,267214de30174dfe88fcd1920754cd793132e833@167.86.115.140:26656,83cbd87e7bbd6afe8ac5359fe9258bef2f79cec2@144.202.124.242:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.nibid/config/config.toml
```

### Задаємо мінімальну ціну за gas
```
sed -i.bak 's/minimum-gas-prices =.*/minimum-gas-prices = "0.025unibi"/g' $HOME/.nibid/config/app.toml
```

### Створюємо сервісний файл
```
sudo tee /etc/systemd/system/nibid.service > /dev/null <<EOF
[Unit]
Description=nibid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nibid) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### І запускаємо сервіс
```
sudo systemctl daemon-reload
sudo systemctl enable nibid
sudo systemctl restart nibid
```

### Дивимося логи і чекаємо коли нода почне синхронізуватися
```
sudo journalctl -u nibid -f -o cat
```

### Або дивимося статус синхронізації (коли "catching_up": false то нода синхронізована)
```
curl localhost:26657/status
```

### Так само можна синхронізуватися з ноди нібіру
```
NETWORK=nibiru-testnet-2
sed -i 's|enable =.*|enable = true|g' $HOME/.nibid/config/config.toml
sed -i 's|rpc_servers =.*|rpc_servers = "'$(curl -s https://networks.testnet.nibiru.fi/$NETWORK/rpc_servers)'"|g' $HOME/.nibid/config/config.toml
sed -i 's|trust_height =.*|trust_height = "'$(curl -s https://networks.testnet.nibiru.fi/$NETWORK/trust_height)'"|g' $HOME/.nibid/config/config.toml
sed -i 's|trust_hash =.*|trust_hash = "'$(curl -s https://networks.testnet.nibiru.fi/$NETWORK/trust_hash)'"|g' $HOME/.nibid/config/config.toml
```

### Після синхронізації створюємо гаманець (не забуваємо зберегти мнемонік)
```
nibid keys add $NIBIRU_WALLET
```

### Якщо гаманець уже є, то відновлюємо за допомогою мнемоніка
```
nibid keys add $NIBIRU_WALLET --recover
```

### Додаємо змінну з адресою гаманця
```
NIBIRU_ADDR=$(nibid keys show $NIBIRU_WALLET -a)
```

### Додаємо змінну в баш профіль
```
echo 'export NIBIRU_ADDR='${NIBIRU_ADDR} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Тепер нам необхідно отримати кошти на гаманець. Для цього переходимо в дискорд і запитуємо токени на баланс гаманця
https://discord.com/invite/xZ36B78jrD
### После запроса проверяем баланс
```
nibid query bank balances $NIBIRU_ADDR
```

### Якщо кошти успішно надійшли, то створюємо валідатора (суму вказуйте свою, скільки хочете делегувати з гаманця, вказуйте трохи менше, щоб вистачило на комісію)
```
nibid tx staking create-validator \
--amount 10000000unibi \
--commission-max-change-rate "0.1" \
--commission-max-rate "0.20" \
--commission-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(nibid tendermint show-validator) \
--moniker $NIBIRU_MONIKER \
--chain-id nibiru-testnet-2 \
--from $NIBIRU_WALLET \
--fees 5000unibi
```

### Задаємо змінну з адресою валідатора
```
NIBIRU_VALOPER=$(nibid keys show $NIBIRU_ADDR --bech val -a)
```

### І додаємо її в баш профіль
```
echo 'export NIBIRU_VALOPER='${NIBIRU_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Перевірка статусу валідатора
```
nibid query staking validator $NIBIRU_VALOPER
```

### Делегація коштів з гаманця на валідатора (суму вводите свою)
```
nibid tx staking delegate $NIBIRU_VALOPER 10000000unibi --from $NIBIRU_WALLET --chain-id $NIBIRU_CHAIN --fees 5000unibi
```

### Якщо раптом ваша нода потрапила у в'язницю, то вихід
```
nibid tx slashing unjail --from $NIBIRU_WALLET --chain-id $NIBIRU_CHAIN --fees 5000unibi
```

### Ну а якщо ви вирішили видалити ноду, то введіть такі команди
```
sudo systemctl stop nibid
sudo systemctl disable nibid
cd $HOME
rm /etc/systemd/system/nibid
rm /usr/local/bin/nibid
rm -r nibiru/
rm -r .nibid/
```

Чат  Telegram Nibiru- https://t.me/nibiru_ru










