# near_stakewars
Установка ноды с нуля




<h2>Покупка сервера </h2>

![image](https://user-images.githubusercontent.com/23613367/180249935-b7188e5c-3a36-460f-8e7d-05f441b44ec7.png) <br>
Для текущей стадии достаточно сервера за 5 евро, потом места будет нехватать скорее все, будем либо докупать либо переезжать на сервер по больше. Либо изначально берем VPSку по требованиям - 4 cores 8 RAM 500gb. Выбираем Ubuntu 20.04 и выбираем бесплатный регион.

<h2>Первоначальная настройка</h2>

<strong><code>sudo apt update && sudo apt upgrade -y</strong></code><br>

Может занять чутка времени, ждем пока все обновится...<br>

Дальше ставим Node.js, и npm:<br>

<code><strong>curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -</code></strong><br>

<strong><code>sudo apt install build-essential nodejs</strong></code><br>

![image](https://user-images.githubusercontent.com/23613367/180254409-c679a737-b10f-4de4-ba79-2e353e23183f.png)<br>

жмем Y и enter:)<br>
И потом вставляем: <br>
<strong><code>PATH="$PATH"</strong></code><br>

<h3> Проверяем версии: </h3><br>

<strong><code>node -v</strong></code><br>

<strong><code>npm -v</strong></code><br>

![image](https://user-images.githubusercontent.com/23613367/180257225-2c527fc7-bff3-4aea-91d0-b303f5316acc.png)<br>

Если все ок, то нода буде 18+ версии и нпм 8+<br>

<h3>Ставим Near CLI (https://github.com/near/near-cli)</h3>
<br>

<strong><code>sudo npm install -g near-cli</strong></code><br>

теперь каждый раз при заходе на сервак если собираетесь взаимодействовать с Near CLI, пишем:<br>

<strong><code>export NEAR_ENV=shardnet</strong></code><br>

Можете потыкать команды:<br>
<strong><code>near proposals</strong></code><br>

<strong><code>near validators current</strong></code><br>

<strong><code>near validators next</strong></code><br>

По названию думаю понятно, что они делают, вот к примеру пропозалы:<br>

![image](https://user-images.githubusercontent.com/23613367/180260701-f1f5f831-328a-4b54-b0a6-af9deebf24f6.png)<br>

<h2>Установка собственно ноды или же Chellenge #2</h2><br>

Если брали на контабо, этого делать не нужно, если же нет, то чекнем подходит ли цпу сервака:<br>

<strong><code>lscpu | grep -P '(?=.*avx )(?=.*sse4.2 )(?=.*cx16 )(?=.*popcnt )' > /dev/null \
  && echo "Supported" \
  || echo "Not supported"
  </strong></code>
  
![image](https://user-images.githubusercontent.com/23613367/180262301-fcb1a732-7197-4213-a79e-bb83eead164e.png)<br>
В нашем случае все ок, получили ответ - Supported :)<br>

<h3>Ставим нужные инструменты</h3>

<strong><code>sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm cargo</strong></code><br>

Ставим Python pip, нажимаем Y если попросит<br>
<strong><code>sudo apt install python3-pip</strong></code><br>
И вводим две команды ниже, по порядку<br>
<strong><code>USER_BASE_BIN=$(python3 -m site --user-base)/bin</strong></code><br>
<strong><code>export PATH="$USER_BASE_BIN:$PATH"</strong></code><br>

Ставим инструменты для билда ноды:<br>
<strong><code>sudo apt install clang build-essential make</strong></code><br>

Теперь ставим Rust & Cargo:<br>
<strong><code>curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh</strong></code><br>

Всплывет что-то типа такого:<br>

![image](https://user-images.githubusercontent.com/23613367/180264917-9a875fc7-951f-4d84-8f0c-7bb6e5d6215e.png)

Жмем 1 и enter :) <br>

Если все ок, то увидим что то такое:<br>

![image](https://user-images.githubusercontent.com/23613367/180265283-7068de43-b5e2-45a0-8b02-39a971d31344.png)

Вставляем:<br>
<strong><code>source $HOME/.cargo/env</strong></code><br>

Теперь клонируем nearcore с гита:<br>
<strong><code>git clone https://github.com/near/nearcore</strong></code><br>
<strong><code>cd nearcore</strong></code><br>
<strong><code>git fetch</strong></code><br>

<strong>Теперь ВАЖНО! Делаем чекаут к нужному комиту, он лежит тут: https://github.com/near/stakewars-iii/blob/main/commit.md</strong>

То есть, git checkout + то что лежит по ссылке выше, на момент написания это будет:

<strong><code>git checkout 0f81dca95a55f975b6e54fe6f311a71792e21698</strong></code>

![image](https://user-images.githubusercontent.com/23613367/180267344-9b1982ea-0e03-4a7d-b7e6-310fac04d4cd.png)

Теперь компилируем бинарник nearcore(займет чутка времени):<br>

<strong><code>cargo build -p neard --release --features shardnet</strong></code>

Теперь нам нужны некоторые файлы конфигурации нашей ноды, если коротко то мы создаем папку дата (туда будут записываться блоки), конфиг файл (который отвечает за то, как будет работать нода, например искать пиры и комуницировать с ними), генезис файл (в нем инфа о старте блокчейна, такой себе типа снепшот блока с которого все стартует), и нод кей файл (там будут ключи ноды и ее айди).<br>

Пишем:<br>

<strong><code>./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis</strong></code>

<strong><code>rm ~/.near/config.json </strong></code>

<strong><code>wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json </strong></code>

<h3>Запускаем ноду</h3>

<strong><code>cd ~/nearcore </strong></code>
  
<strong><code>./target/release/neard --home ~/.near run </strong></code>

![image](https://user-images.githubusercontent.com/23613367/180310102-2632130c-5bbe-4cc4-9ebc-826c145371e9.png)

Должны забегать буковки, цифры и т.д

Нода найдет пиры, потом скачает хедеры, потом начнет качать блоки, Вы можете ждать пока докачает блоки до 100% и пойти выпить пива, либо Ctr+C и продолжаем

<h3>Запускаем валидатора</h3>

Пишем:<br>

<strong><code>near login</strong></code>

Если желтым текстом будут просить Collect data, можете жать Y, Дальше у нас будет ссылка, вот типа такая:<br>

![image](https://user-images.githubusercontent.com/23613367/180310884-8dfcd329-09ab-4400-a32f-bbd5de612a2b.png)<br>

Сначала регистрируем себе кошелек тут:<br>

<strong>https://wallet.shardnet.near.org/</strong>

Дальше нам нужно пробросить ssh тунель к серваку, я буду использовать 3й способок этого мануала https://putty.org.ru/articles/putty-ssh-tunnels.html
тоесть поднимать тунель как прокси, и зайду через инкогнитон под проксей, именно в браузере под тунелем мы переходим по ссылке полученой в консоле. Вы можете искать на ютубе любой способо как пробросить тунель...

После того как мы подключили ранее сделанный кошелек, нас кинет сюда:<br>

![image](https://user-images.githubusercontent.com/23613367/180313257-b2be678f-4229-4f0c-8f22-4a7da9065447.png)<br>

Все ок, все получилось, в консоль если не получили зеленое successfully, то вводим id(то что мы вводили когда вязали кош к ноде, что то типа yournode.shardnet.near)

Теперь нам нужно чекнуть validator key file, скорее всего его не будет<br>
<strong><code>cat ~/.near/validator_key.json</strong></code><br>

Если как у меня, то едем дальше, если нет - пропускаем:<br>

![image](https://user-images.githubusercontent.com/23613367/180313993-037a2b93-423e-4cca-b49a-2a4aadb11b9b.png)<br>

<code><strong>near generate-key <pool_id></code></strong><br>
 
вместо pool_id xx.factory.shardnet.near, например nodeemperor.factory.shardnet.near<br>
  
  <strong><code>cp ~/.near-credentials/shardnet/YOUR_WALLET.json ~/.near/validator_key.json</strong></code>
  
Теперь пишем:<br>
  <strong><code>nano ~/.near/validator_key.json</strong></code><br>
  account id, меняем на xx.factory.shardnet.near вместо xx имя вашего пула.<br>
  И вместо private_key пишем secret_key. CTRL+X жмем Y и энтер
  
  Должно получится как_то так:<br>
  
  ![image](https://user-images.githubusercontent.com/23613367/180315995-558eabf2-6bdb-4e83-8bf9-4a5e01ff7740.png)<br>
  
  <h3>Делаем сервис</h3>
  <strong><code>sudo nano /etc/systemd/system/neard.service</strong></code><br>
  Вставляем:<br>
<strong>
<code>
      [Unit]
Description=NEARd Daemon Service

[Service]<br>
      
Type=simple<br>
      
User=<USER-ВЕЗДЕ МЕНЯЕМ НА ВАШЕГО ЮЗЕРА,В МОЕМ СЛУЧАЕ root, если у вас тоже рут ниже в путях сотрите home(потому-что у нодранеров нет дома:)><br>
      
#Group=near<br>
      
WorkingDirectory=/home/<USER>/.near<br>
      
ExecStart=/home/<USER>/nearcore/target/release/neard run<br>
      
Restart=on-failure<br>
      
RestartSec=30<br>
      
KillSignal=SIGINT<br>
      
TimeoutStopSec=45<br>
      
KillMode=mixed<br>
      
<br>
[Install]<br>
      
WantedBy=multi-user.target<br>
</strong></code>
 
  
  
<h3>Команды</h3>
  <strong><code>sudo systemctl enable neard</strong></code> (Активация сервиса) <br>   
  <strong><code>sudo systemctl start neard</strong></code>(Старт сервиса)<br>
  <strong><code>sudo systemctl reload neard</strong></code>(Если что-то факапнули в файле сервиса, то исправляем и релоадим<br>
  <strong><code>journalctl -n 100 -f -u neard</strong></code> (Логи)<br>
  Логи в красивом принте:<br>
  <strong><code>sudo apt install ccze </strong></code><br>
  <strong><code>journalctl -n 100 -f -u neard | ccze -A </strong></code><br>
  
  <h2>Создаем стейкинг пул, заливаем туда токенов и попадаем в актив сет</h2>
  Ждем пока нода синхронизируется на 100%, а пока ждем можем чутка хрононизироваться и попить пивка :)
  Чтобы понять это идем просто на https://explorer.shardnet.near.org/blocks и смотрим по логам на каком вы блоке )
  <h3>Сoздаем пул</h3>
  
<strong><code>near call factory.shardnet.near create_staking_pool '{"staking_pool_id": "<pool id>", "owner_id": "<accountId>", "stake_public_key": "<public key>", "reward_fee_fraction": {"numerator": 5, "denominator": 100}, "code_hash":"DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ"}' --accountId="<accountId>" --amount=30 --gas=300000000000000
 </strong></code><br>
Нужно поменять<br>
Pool ID, Owner ID, Public Key, Account Id<br>
    
Паблик кей можно найти в validator_key.json<br>
    
Теперь депозитим все ниры с кошелька на наш пул с помощью команды:<br>
<code>near call <staking_pool_id> deposit_and_stake --amount <amount> --accountId <accountId> --gas=300000000000000</code><br>значения как и раньше меняем на свои, оставляю пару ниров для комс<br>
    
    
<h3>Ping</h3>
Делаем крона, потому-что мы обязаны пинговать свой пул каждую эпоху
      <strong><code>nano near_ping.sh</strong</code>
        Туда пишем:<br>
        <strong><code>NEAR_ENV=shardnet near call XXX.factory.shardnet.near ping '{}' --accountId XXX.shardnet.near --gas=300000000000000 </strong></code>
        как обычно меняем данные на свои<br>
        
И <code>nano near_ping.log</code><br>
там пишем #start<br>
        
<code>crontab -e</code><br>
        
Туда запихаем:<br>
        
<code>*/5  * * * * nohup ~/near_ping.sh >> ~/near_ping.log 2>&1 &</code><br>

И пишем:<br>
        
<code>chmod +x near_ping.sh</code><br>
Сразу после этого можете найти себя тут https://explorer.shardnet.near.org/nodes/validators <br>
        Будет писать Proposal, потом Joining, а потом если все ок Active </br>

        
![image](https://user-images.githubusercontent.com/23613367/180327173-79e57ff6-4361-41fb-bbac-fff104d2cff4.png)<br>

      
<h2>Полезные команды проверки ноды</h2>
        
1.Логи - journalctl -n 100 -f -u neard | ccze -A (Лежат логи тут - ~/.nearup/logs)
        
2.РПЦ команды, ( можно найти много тут https://docs.near.org/docs/api/rpc )
        
3. Проверить версию ноды - <code> curl -s http://127.0.0.1:3030/status | jq .version </code>
        
4.Проверить делегации и стейк <code> near view <your pool>.factory.shardnet.near get_accounts '{"from_index": 0, "limit": 10}' --accountId <accountId>.shardnet.near </code>
 
5.Одна из важных, когда вылетаем из активного сета, проверить причину:<br>
        
<code> curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.prev_epoch_kickout[] | select(.account_id | contains ("<POOL_ID>"))' | jq .reason </code>
 
  

 













