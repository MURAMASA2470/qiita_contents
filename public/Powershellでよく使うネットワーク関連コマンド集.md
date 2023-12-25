---
title: Powershellでよく使うネットワーク関連コマンド集
tags:
  - Network
  - トラブルシューティング
  - PowerShell
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## トラブルシューティング編

### IP構成情報を表示

```powershell
ipconfig /all
```

### ホストへの応答確認

```powershell
ping [ホスト名またはIPアドレス]
```

### パケットの経路を表示

```powershell
tracert [ホスト名またはIPアドレス]
```

### DNS情報を取得

```powershell
nslookup [ドメイン名またはIPアドレス]
```

### ポートの接続テストを実行

```powershell
Test-NetConnection -ComputerName [ホスト名またはIPアドレス] -Port [ポート番号]
```

### ホストへのパケット送信と応答の確認

```powershell
Test-Connection [ホスト名またはIPアドレス]
```

### ネットワークアダプタの情報を表示

```powershell
Get-NetAdapter
```

### ルーティングテーブルの情報を表示

```powershell
Get-NetRoute
```

## 番外編

### 過去に接続したことのあるWi-Fiルーターのパスワードを忘れた！

```powershell
# 過去に接続したSSIDの一覧を表示
netsh wlan show profile

# 指定したSSIDの記憶しているパスワードを表示
# セキュリティの設定 > 主要なコンテンツ に表示されているのがパスワード
netsh wlan show profile key=clear [パスワードを確認したいSSID]
```

### 今どのネットワークに接続しているの？

```powershell
# 現在接続中のネットワークの情報を表示
netsh wlan show interfaces
```

### 現在受信しているWi-Fiにはどんなものがある？

```powershell
# 現在受信しているWi-Fiの電波をBSSID含めてすべて表示
netsh wlan show networks mode=bssid
```

### このIPアドレス/ホスト名は誰のもの？

WindowsのNBT（NetBIOS over TCP/IP）プロトコルを利用して、逆引きします。

```powershell
# IPアドレスからホスト名(NETBIOS名)を逆引き
nbtstat -A [IPアドレス]

# ホスト名(NETBIOS名)からIPアドレスを逆引き
nbtstat -a [ホスト名]

```