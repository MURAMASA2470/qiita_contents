---
title: AppcmdでPowerShellからIISを操作する
tags:
  - IIS
  - appcmd
  - deploy
  - PowerShell
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---


## Appcmdについて

### 概要

IISの構成や操作に関する多くのタスクを、コマンドラインから実行するために使用されるツールで、IIS 7.0以降のバージョンで利用出来ます。
コマンドラインツールなので、バッチファイルやスクリプトに組み込むことで、処理の自動化などが可能になっています。

### 出来ること

`Appcmd`で行える操作には以下のようなものがあります。
- サイトの作成・設定
- アプリケーションプールの作成・設定
- サイトやアプリケーションプールの一覧表示
- サイトやアプリケーションプールの状態の変更（開始、停止など）
- 構成のバックアップや復元


## Appcmdの場所

`Appcmd`はWindowsの以下の場所に配置されています。

```powershell
C:\Windows\System32\inetsrv\Appcmd.exe
```

## 一覧表示系

### サイトの一覧表示

```powershell
appcmd list site
```

### アプリケーションプールの一覧表示

```powershell
appcmd list apppool
```

## アプリケーションプール関連

IISにはデフォルトで`DefaultAppPool`や`.NET 2.0`、`.NET 4.5`等のアプリケーションが作成されています。
`.Net CLR バージョン`という列の値が`v2.0`の場合は`.NET Framework 3.X`、`v4.0`の場合は`.NET Framework 4.X`のアプリケーションがそれぞれ対応しています。

### アプリケーションプールの作成

```powershell
appcmd add apppool /name:"MyAppPool"
```

- `/name`
  アプリケーションプール名


### アプリケーションプールの削除

```powershell
appcmd delete apppool "MyAppPool"
```


## サイト関連

IISにはデフォルトで`Default Web Site`という名称でサイトが作成されています。

### サイトの作成

```powershell
appcmd add site /name:"MySite" /bindings:http://localhost:80 /physicalPath:"C:\サイトまでの物理パス"
```

- `/name`
  サイト名
- `/bindings`
  待ち受けるドメインとポート番号
- `/physicalPath`
  コンテキストルートとする物理パス


### サイトの削除

```powershell
appcmd add site "MySite"
```

### サイトの停止

```powershell
appcmd stop site "MySite"
```

### サイトの開始

```powershell
appcmd start site "MySite"
```

### サイトとアプリケーションプールの関連付け

```powershell
appcmd set app "MySite/" /applicationPool:"MyAppPool"
```

- `/applicationPool`
  アプリケーションプール名


## おまけ

以下のコマンドで、IISそのものを停止/開始することが出来ます。

```powershell
# IISの開始
net start w3svc

# IISの停止
net stop w3svc
```