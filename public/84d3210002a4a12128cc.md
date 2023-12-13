---
title: IBM i(旧AS400)のQSHでSSHが実行できない時に確認すること
tags:
  - SSH
  - QShell
  - '#IBMi'
  - '#AS400'
private: false
updated_at: '2023-12-11T00:47:22+09:00'
id: 84d3210002a4a12128cc
organization_url_name: null
slide: false
ignorePublish: false
---

## ホストコードページの設定を確認

使用するクライアントアプリによって設定する場所は異なりますが、IBM i Access Client Solutionsでは以下の「ホスト・コード・ページ」という箇所を「939 日本（拡張ローマ字）」に設定する必要があります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/c75488a1-3ab7-1fe1-28b1-0fdc847efb02.png)

## CCSIDの設定を確認

文字化けを防ぐ為に、CCSIDを`5035`に設定する必要があります。

```bash
CHGJOB CCSID(5035)
```

## サインイン中のユーザーIDが8文字以下になっているか確認

IBM iのソフトウェアの多くが[AIX OS](https://ja.wikipedia.org/wiki/AIX)向けに作成されている為、ユーザーIDが8文字を超えることが基本的に想定されていません。
もし8文字を超える場合、メモリのバッファオーバーフローなどが発生する可能性がある為、あえて制限されています。

どうしても8文字を超えるユーザーIDを使用したい場合は、`/QOpenSys/QIBM/UserData/SC1/OpenSSH/etc/sshd_config`に以下の設定を追加するか

```config:sshd_config
ibmpaseforienv PASE_USRGRP_LIMITED=N
```

以下のコマンドを実行することで、10文字まで使用可能になります。

```bash
ADDENVVAR ENVVAR(PASE_USRGRP_LIMITED) VALUE('N') LEVEL(*SYS)
```
