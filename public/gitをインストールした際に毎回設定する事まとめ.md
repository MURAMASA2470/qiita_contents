---
title: gitをインストールしたらやるべきこと
tags:
  - Git
  - 初回
private: false
updated_at: '2023-12-07T02:13:53+09:00'
id: 46dc2b8130de153f596c
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
Gitをインストールした際に、毎回設定している事をまとめました。
すべて必要というわけではないので、各自お好みで設定してみてください。

# 設定一覧

## ユーザー名とメールアドレスの設定

まずはお馴染みの設定から。
コミットログとして表示されるユーザー名とメールアドレスを設定します。

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

適当な名前やメールアドレスでも登録できますが、基本的にはgithubなどに登録している情報と合わせるようにしましょう。

## デフォルトのエディタの設定

コミットする際などに表示されるエディタを変更します。

```bash
git config --global core.editor vim
git config --global sequence.editor vim
```

この`vim`の部分には他に、`emacs`, `nano`, `'code --wait'`, `hx`, `micro`などが指定できます。
※`'code --wait'`はVSCodeの事です。末尾の`--wait`オプションを指定するとファイルを閉じるまで待機させることが出来ます。

## デフォルトのブランチ名の変更

デフォルトで作成されるブランチ`main`に変更します。

```bash
git config --global init.defaultBranch main
```
`git init`を実行した際に自動的に`master`ブランチが作成されてますが、現在は`main`を使うことが多く、毎回ブランチを削除するのは面倒ですよね。

※Gitのバージョンが 2.28.0より前の場合、この機能は利用できません。

## 改行コードの設定

改行コードを自動的に変換するようにします。

```bash
# Mac及びLinuxの場合 (LF)
git config --global core.autocrlf input

# Windowsの場合 (CRLF)
git config --global core.autocrlf true
```
チームで開発している場合、OSがmacの人もいればWindowsの人もいます。
そうした場合に改行コードの問題はつきものですが、自動変換をオンにすることで、意識することなく進められます。
チーム全員がWindowsの場合は`false`と指定しても問題ありません。


## コミットメッセージにテンプレートを設定

毎回お決まりで入力しているメッセージがある場合、テンプレートを利用することで、`git commit`を実行した際にテンプレートを呼び出すことが出来ます。

```bash
git config --global commit.template ~/.config/git/.gitmessage
echo 'テンプレート化したいメッセージ' > ~/.config/git/.gitmessage

# プロジェクト毎に指定したい場合
git config --local commit.template .gitmessage
echo 'テンプレート化したいメッセージ' > .gitmessage
```

チーム開発などで、コミットメッセージのフォーマットを徹底したい場合に便利です。

## グローバルな.gitignoreの設定

`.gitignore`をグローバルに設定します。
デフォルトでは`~/.config/git/ignore`の内容が反映されます。

```bash:~/.config/git/ignore
*~
.DS_Store
```
`*~`はExcelなどのロックファイル、`.DS_Store`はMacお馴染みのファイルです。


任意のファイルを指定したい場合は以下のように指定出来ます。

```bash
git config --global core.excludesfile ~/.gitignore
```

基本的にはプロジェクト毎に管理することが多い為、利用している人は少ない印象です。


## コマンドのタイプミス時にいい感じに実行してくれるように設定

コマンドの綴りミスがあった際にGitは正しいコマンドを提案してくれますが、実行までしてくれるようになります。
ミスタイプから実行までの感覚を0.1秒単位で指定します。

```bash
git config --global help.autocorrect 1
```
私はよく`checkout`をミスタイプすることが多いです。


# 参考

https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA-Git-%E3%81%AE%E8%A8%AD%E5%AE%9A

https://qiita.com/ucan-lab/items/9b442e042988e2d7a35d

https://qiita.com/ueokande/items/e0409219e7c68e4277b9

