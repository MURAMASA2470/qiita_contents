---
title: WSLのインストールエラーの時に確認すること
tags:
  - Windows
  - WSL
  - WSL2
private: false
updated_at: '2023-12-10T20:26:27+09:00'
id: eff7d510c7f0539738cf
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

WSLでubuntuなどのディストリビューションをインストールしようとすると、以下のようなエラーが出ることがあります。
このエラーの原因は多岐にわたり、なかなか状況を改善できないことが多いです。
そういう時に、以下の内容を確認してみてください。

```
Installing, this may take a few minutes...
WslRegisterDistribution failed with error: 0xXXXXXXXX
Error: 0xXXXXXXXX ??????????????????????????????????????
```

# 確認すること

## 管理者権限のPowerShellで実行しているか？

そもそもですが、WSLをインストールする際は、管理者権限を持つPowerShellで実行する必要があります。

## Windows Updateが溜まっていないか？

Windowsのバージョンが古い場合、WSLが正常に動作しないことがあります。
更新プログラムを確認して、適応していないパッチがある場合はWindows Updateをインストールしてください。

## BIOS/UEFIのバージョンが古くないか？

古いBIOS/UEFIバージョンでは、仮想化機能や関連する機能が正しくサポートされていないことがあります。
自身のPCメーカーのサポートページから最新のBIOS/UEFIアップデートを確認し、アップデートを行ってください。

## CPUの仮想化機能が有効になっているか？

BIOS/UEFI設定画面で、仮想化機能（Intel VT-xまたはAMD-V）が有効になっていることを確認してください。
Intel製CPUの場合は「Virtualization Technology」、AMD製CPUの場合は「SVM Mode」といった名前で表示されます。

マザーボードのメーカーによってBIOS内の項目の場所は異なりますが、大体以下のようになっているはずです。

Intel製：
*「System Configuration」->「Virtualization Technology」->「Enabled」*

AMD製：
*「Advanced」 -> 「CPU Configuration」 -> 「SVM Mode」->「Enabled」*

## Hyper-Vは有効になっているか？

WSL 2を利用するためには、Hyper-Vが有効になっている必要があります。コントロールパネルの「プログラムと機能」から「Windowsの機能の有効化または無効化」に移動し、Hyper-Vを有効にしてください。

以下のコマンドでHyper-Vを有効化できます。

```bash
dism.exe /online /enable-feature /featurename:HypervisorPlatform /all /norestart
```
※PCの再起動が必要です。

## WSLのバージョンが2になっているか？

WSL 2を利用するには、適切なバージョンにアップグレードする必要があります。以下のコマンドを使用してWSL 2にアップグレードできます。

```bash
# インストールされている各ディストリビューションのバージョンを確認
wsl --list --verbose

# 上記でバージョンが1となっているものがあれば、バージョンを2に変更する
wsl --shutdown
wsl --set-version <ディストリビューションの名前> 2
```

## WSLのバージョンが古くないか？

以下のコマンドで、WSLを最新化できます。
```bash
wsl --update
```

## PC本体のメモリは足りているか？

WSLはメモリを多く消費します。タスクマネージャーなどで確認して、十分なメモリ量が使用可能であることを確認してください。
PC本体のメモリ上限にもよりますが、大体2GB～4GB程度消費されます。

## PC本体のストレージは足りているか？

WSLはかなりのストレージ容量を占有します。十分な空き容量を確保してください。
WSL本体は約1GB程度で、ubuntuなどのディストリビューションは5, 6GBほど占有します。
ディストリビューション内でDockerを使用している場合は、いつの間にか100GBを超えているなんてこともあるので注意しましょう。

以下のコマンドで、指定したディストリビューションの容量を確認することが出来ます。
※`Used`が占有している容量です。

```bash
wsl --system -d <ディストリビューションの名前> df -h /mnt/wslg/distro

# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sdc        251G   42G  197G  18% /mnt/wslg/distro
```

# それでも解決しない場合

以上のことすべてを確認しても状況が改善しない場合は、WSLの再インストールをしてみるのも1つの手です。

以下の3機能を1度無効化した後に、再度有効化します。
- 仮想マシンプラットフォーム
- Linux 用 Windows サブシステム
- Windows ハイパーバイザー プラットフォーム


```bash
# 機能を無効化
dism.exe /online /disable-feature /featurename:VirtualMachinePlatform /norestart
dism.exe /online /disable-feature /featurename:Microsoft-Windows-Subsystem-Linux /norestart
dism.exe /online /disable-feature /featurename:HypervisorPlatform /norestart
# PCを再起動
Restart-Computer -Force

# 機能を有効化
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:HypervisorPlatform /all /norestart
# PCを再起動
Restart-Computer -Force
```

# 参考

https://zenn.dev/h4m4chi/articles/8627893de9cdc5

