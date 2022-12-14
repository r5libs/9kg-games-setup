## 必要環境 - MySql

```bash
DB_HOST: 192.168.1.114
DB_USER: 9kg
DB_PASSWORD: 5NGjD35KwE8HFstp
DB_NAME: 9kg
DB_PORT: 8869
```

```bash
DB_HOST: 192.168.1.114
DB_USER: 9kg
DB_PASSWORD: 5NGjD35KwE8HFstp
DB_NAME: host
DB_PORT: 8869
```

## 必要環境 - Redis

```bash
Port: 6379
```

```bash
如果 Redis 需要對外開放(非localhost也可以存取):

1. 編輯檔案 sudo vi /www/server/redis/redis.conf (可能在不同路徑)
# 要把這行註解, 預設只有localhost才可以存取
# bind 127.0.0.1

2. 重啟 systemctl restart redis

3. 查看使否重啟成功 systemctl status redis
```

## 必要環境 - Conda (Python 執行環境)

```bash
安裝 Conda (假設是 /home/xxx 路徑):
curl https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh -o /home/9kg-server/installer.sh && chmod 755 /home/9kg-server/installer.sh && /home/9kg-server/installer.sh && rm -f /home/9kg-server/installer.sh

讓 Conda 命命立即生效:
source ~/.bashrc
```

```bash
使用安裝包(9kg-server.zip), 安裝各 server 的執行環境: (這裡假設把 9kg-server.zip 解壓縮至 /home/9kg-server/9kg-server)

conda env create --name apiserver_env -f /home/9kg-server/apiserver_envs.yaml

conda env create --name gameserver_env -f /home/9kg-server/gameserver_envs.yaml

conda env create --name stageserver_env -f /home/9kg-server/stageserver_envs.yaml

conda env create --name innerhostapiserver_env -f /home/9kg-server/innerhostapiserver_envs.yaml

conda env create --name 9kg-api-backend_env -f /home/9kg-server/9kg-api-backend_envs.yaml
```

## Server 設定

```bash
[stageserver]
Port: 5114
```

```bash
[innerhostapiserver]
Port: 5113
```

```bash
[apiserver]
Port: 5119
```

```bash
[gameserver]
Port: 5118 (需對外開放)

sudo firewall-cmd --zone=public --add-port=5118/tcp --permanent
sudo systemctl restart firewalld
sudo firewall-cmd --zone=public --list-ports --permanent
```

## Server 啟動次序

```bash
screen -dmS stageserver
screen -r stageserver
conda run -n stageserver_env --live-stream --cwd /home/9kg-server/stageserver python ./stageserver.py

conda activate stageserver_env && cd stageserver && python ./stageserver.py
```

```bash
screen -dmS innerhostapiserver
screen -r innerhostapiserver
conda run -n innerhostapiserver_env --live-stream --cwd /home/9kg-server/innerhostapiserver python ./innerhost.py

conda activate innerhostapiserver_env && cd innerhostapiserver && python ./innerhost.py
```

```bash
screen -dmS apiserver
screen -r apiserver
conda run -n apiserver_env --live-stream --cwd /home/9kg-server/apiserver python ./apiserver.py

conda activate apiserver_env && cd apiserver && python ./apiserver.py
```

```bash
screen -dmS gameserver
screen -r gameserver
conda run -n gameserver_env --live-stream --cwd /home/9kg-server/gameserver python ./gameserver.py

conda activate gameserver_env && cd gameserver && python ./gameserver.py
```

```bash
screen -dmS 9kg-api-backend
screen -r 9kg-api-backend
conda run -n 9kg-api-backend_env --live-stream --cwd /home/9kg-server/9kg-api-backend python ./apibackend.py

conda activate 9kg-api-backend_env && cd 9kg-api-backend && python ./apibackend.py
```

## 注意事項

```bash
啟動 server 之後, 需要執行以下命令載入相關資料: (因為是在本機, 所以直接使用127.0.0.1即可)

curl -X GET http://127.0.0.1:5114/reload_hostsetting

curl -X GET http://127.0.0.1:5119/reload_gametable_to_redis/

curl -X GET http://127.0.0.1:5119/reload_hostsetting_to_redis/
```
