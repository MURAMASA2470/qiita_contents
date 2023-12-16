---
title: Windows IISでFTPサーバーを立てる
tags:
  - ftp
  - FTPサーバ
  - Widnows
private: false
updated_at: '2023-03-28T02:29:09+09:00'
id: 819df8e1069737deb281
organization_url_name: null
slide: false
ignorePublish: false
---

# この記事で出来るようになること
- IISでFTPサーバーを立てる
- ユーザー毎にアクセス可能なディレクトリを制限する
- 見せたくないフォルダだけ非表示にする

# FTPサーバーの設定

IISを起動し、FTPサイトを追加します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/064a04bc-8f03-b0d8-b42d-e95ebeee878f.png)

FTPサイト名 と 物理パス を入力します。
今回はFTPのルートを`C:\inetpub\ftproot\`とします。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/df2f0af6-f7a7-3fc2-4c9e-e6842743250c.png)

SSLは無し(必要に応じて設定してください)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/8b8200b2-f1bc-47b5-a7ba-a54273096424.png)

認証は基本を選択し、アクセス許可を「すべてのユーザー」、読み書きあり で設定します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/8162f147-50ad-c4cb-f96c-465a8dcd417c.png)

FTPサイトが追加されたことを確認します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/e9408ff7-4abc-fe03-665f-d15e6f68ceb2.png)

# FTPユーザーの作成

**管理者権限**でPowershellを開き、以下のコマンドを実行します。
```powershell:powershell
# net user [ユーザー] [パスワード] /add
net user ftp-user ftp-user /add
```

FTPルート配下にユーザーフォルダを作成し、権限を付与します。
※ユーザーフォルダの親フォルダである`LocalUser`の名称は固定です。
```powershell:powershell
# FTPサーバーのルートディレクトリへ移動
cd C:\inetpub\ftproot\

# ユーザーフォルダの作成
mkdir -p LocalUser\ftp-user

# ユーザーフォルダに変更権限を付与
icacls LocalUser\ftp-user /grant ftp-user:`(OI`)`(CI`)M
```

権限が`ftp-user`に設定されていることを確認します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/9149fac3-54c2-25eb-3fde-3b0d496f6577.png)


FTPコマンドで接続の確認を行います。
```powershell:powershell
# FTPで接続の確認
ftp

ftp> open 127.0.0.1
# 127.0.0.1 に接続しました。
# 220 Microsoft FTP Service
# 200 OPTS UTF8 command successful - UTF8 encoding now ON.
ユーザー (127.0.0.1:(none)): ftp-user
# 331 Password required
パスワード: ftp-user
# 230 User logged in.
```

# ユーザーの分離設定

## 概要
ユーザーの分離設定は、ユーザー毎に見える範囲を制限することができ、他人のフォルダを参照出来ないようにします。
また、ユーザーフォルダをルートディレクトリとすることにより、上位の階層へのアクセスを遮断します。

## 設定方法

FTPホームから「FTPユーザーの分離」を開きます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/dee7a96d-6483-95dc-3d5d-d24a44fcc7ee.png)

「ユーザー名ディレクトリ(グローバルな仮想ディレクトリを無効にします)」を選択し、適用します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/a5c63cb2-69fc-f5c5-37c4-a202b4ebe553.png)

適当なフォルダを作成し、ユーザーフォルダより上位のフォルダが参照できないことを確認します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/3f23a0b9-0cbc-22aa-b4d2-a79c23774dd9.png)

```powershell
# 230 User logged in.
ftp> ls
# 200 PORT command successful.
# 125 Data connection already open; Transfer starting.
# folder
# 226 Transfer complete.
# ftp: 11 バイトが受信されました 0.00秒 11000.00KB/秒。
ftp> pwd
# 257 "/" is current directory.
ftp> cd ..
# 250 CWD command successful.
ftp> pwd
# 257 "/" is current directory.
```

以上でユーザーの分離設定の説明は終わりです。


# 非表示セグメントの設定

## 概要
非表示セグメントは、特定の見せたくないフォルダなどを非表示にすることが出来ます。

## 設定方法
まず適当に非表示にしたいフォルダを作成し、先ほど作成したフォルダが見れることを確認します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/920cfb38-c011-08bc-254c-fe106df705b9.png)

```powershell
ftp> ls
# 200 PORT command successful.
# 125 Data connection already open; Transfer starting.
# folder
# 見せられないよ
# 226 Transfer complete.
# ftp: 34 バイトが受信されました 0.00秒 34.00KB/秒。
```

次にFTPホームから「FTP要求フィルター」を選択します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/fa1d6138-5df8-86aa-e0ec-47b1ae8d2a35.png)

コンテキストメニューから「非表示セグメントの追加」を選択します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/bc107b87-3f0a-1940-b3af-b3ca8ad156b5.png)

非表示にしたいファイル名を入力し、OKを押します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/852434b4-c3be-3c52-3bd2-d6d6e7bdf37b.png)

再度FTPでアクセスし、非表示セグメントに設定したフォルダが表示されなくなったことを確認します。
```powershell
ftp> ls
# 200 PORT command successful.
# 125 Data connection already open; Transfer starting.
# folder
# 226 Transfer complete.
# ftp: 11 バイトが受信されました 0.00秒 11000.00KB/秒。
```

以上で非表示セグメントの説明は終わりです。
