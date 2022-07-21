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

![image](https://user-images.githubusercontent.com/23613367/180310884-8dfcd329-09ab-4400-a32f-bbd5de612a2b.png)

Дальше нам нужно пробросить ssh тунель к серваку, я буду использовать 3й способок этого мануала https://putty.org.ru/articles/putty-ssh-tunnels.html
тоесть поднимать тунель как прокси, и зайду через инкогнитон под проксей, именно в браузере под тунелем мы переходим по ссылке полученой в консоле.














