---
title: WebAssemblyに特化したプログラミング言語「Onyx」にWindowsで入門してみる
tags:
  - HelloWorld
  - WebAssembly
  - wasm
  - onxy
private: false
updated_at: '2023-12-25T09:33:50+09:00'
id: ff4e47a65354a4ef5476
organization_url_name: null
slide: false
ignorePublish: false
---

## Onyxとは

WebAssembly(wasm)に特化した新しいプログラミング言語です。

コンパイラはC言語で記述されており、高速なコンパイルを実現しています。
その為、C言語のネイティブライブラリを呼び出すことも可能です。

LLVM や Binaryan などのコンパイラ基盤を使用せず、直接 wasmバイナリにコンパイルするのが特徴です。

ガベージコレクションは無く、メモリの割り当てと解放を意識する必要があり少々上級者向けの言語となっています。

以下のような感じで記述します。

```js
use core { printf, iter }

main :: () {
    for i: 1 .. 10 {
        fact := factorial(i);
        printf("{}! = {}\n", i, fact);
    }
}

factorial :: (n: i32) -> i32 {
    return iter.as_iter(1 .. n)
        |> iter.fold(1, (x, y) => x * y);
}
```

https://onyxlang.io

https://github.com/onyx-lang/onyx

## インストール

1． 公式のGithubリポジトリから、`onyx-windows-wasmer-amd64.zip`というファイルをダウンロードして解凍します

https://github.com/onyx-lang/onyx/releases

2． 解凍したら`onyx-windows-wasmer-amd64`をCドライブ直下に配置します。(`C:\onyx`)

3． `PATH`と`ONYX_PATH`の2つの環境変数に配置したパスを設定します。

```powershell
$path = "C:\onyx\"
# PATHにパスを追加
$newPath = [Environment]::GetEnvironmentVariable("PATH", "User") + ";$path"
[Environment]::SetEnvironmentVariable("PATH", $newPath, "User")

# ONYX_PATHにパスを設定
[Environment]::SetEnvironmentVariable("ONYX_PATH", $path, "User")
```

4．Powershellを開き、`onyx`と入力し実行して以下のように表示されればOKです。

```
Usage:
    onyx <subcommand>

Subcommands:
    help      Shows this help message. Use "onyx help <subcommand>".
    build     Compiles an Onyx program into an executable.
    run       Compiles and runs an Onyx program, all at once.
    check     Checks syntax and types of an Onyx program.
    package   Package manager
    version   Prints version information
```

### VSCodeの拡張機能

補完までは対応していないものの、シンタックスハイライトの対応が出来ます。

https://marketplace.visualstudio.com/items?itemName=onyxlang.onyxlang

## とりあえず"Hello World"

VSCodeなどで`hello.onyx`というファイルを作成し、以下のコードを記載します。

```js:hello.onyx
use core {*}

main :: () {
  printf("Hello World");
}
```

以下のコマンドで実行できます。

```powershell
onyx run .\hello.onyx
# => Hello World
```

## ブラウザで実行してみる

### プログラムの作成

足し算を行うプログラムを作成し、ブラウザのコンソール上に出力してみます。

```js
use core {*}

#export "add" (x, y: i32) -> i32 {
  return x + y;
}

main :: () {}
```

### コンパイル

以下のコマンドで、プログラムをコンパイルします。
コンパイルすると`add.wasm`というファイルが生成されます。

```powershell
onyx build .\add.onyx -o add.wasm -r js
```
※末尾の`-r js`を付けないと、Onyx独自のバイナリが生成されてしまう為、ブラウザのWebAssembly APIで動作しません。

### JavaScriptから呼び出す

以下のファイルを作成し、Webサーバーで起動してください。
Webサーバーがすぐ用意できない場合は、VSCodeの以下の拡張機能をしようすると直ぐ実行可能です。

https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer

```html
<!DOCTYPE html>
<html>
<body>
  <h1>Onyx</h1>

  <script>
    WebAssembly.instantiateStreaming(fetch('add.wasm'),
    {
      // ここは必須
      host: {
        print_str: () => {},
        time: () => {},
      },
    }).then(
      (obj) => {
        // ここに処理を書く
        console.log(obj.instance.exports.add(10, 20));
      }
    )
  </script>
</body>
</html>
```

### 動作確認

30 と表示されていますね。成功です。

![image-1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/a2a9ee73-e654-836f-0684-a80934dfa3b7.png)


## さいごに

Onyxはまだ正式リリースおらず、現状はBeta版となっています。
このまま無事に正式リリースしてほしいですね。
WebAssemblyの発展に、Onyxが寄与する様に期待しましょう。

## 参考

https://zenn.dev/hatappo/articles/f1ecb49715ea6c

https://zenn.dev/01397/scraps/95dc333cb51dfb
