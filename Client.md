## 新增 MIME 類型

```bash
副檔名: .atlas
MIME 類型: text/atlas
```

## 遊戲 Client 架設

```bash
將安裝包(9kg-client.zip) 解壓縮至站台資料夾(如: /www/9kg-client)
```

```bash
編輯檔案 vi /www/9kg-client/game/skm0923001/game.min.js

將 (行號 55348) ws://192.168.1.114:5111/gameserver 的 192.168.1.114 修改為站台域名
須注意站台如果是 https, 則 ws 也需改為 wss
```
