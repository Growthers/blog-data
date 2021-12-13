---
title: "cargo-competeでAtCoder"
date: 2021-12-13
---

この記事は共同開発鯖 Advent Calendar 2021 13日目の記事です。
https://qiita.com/advent-calendar/2021/growthers  

## はじめに

AtCoderをRustでCLIでやりたかったのでその記録です。

## cargo-competeのインストール
```sh
$ cargo install cargo-compete
```
`cargo install`はバージョンの更新をするのに向いてないので`cargo-update`も入れる。
```sh
$ cargo install cargo-update
$ cargo install-update --all # このコマンドで確認と更新ができる
```

## 使い方
詳しいことは[cargo-competeのREADME.md](https://github.com/qryxip/cargo-compete/blob/master/README-ja.md)を参照。

### 初期設定

任意のディレクトリで下記を実行

特に理由がなければ2を選択
(おそらく3を選択すれば任意のrustのバージョンとcrateが使える)

```sh
$ cargo compete init atcoder
Do you use crates on AtCoder?
1 No
2 Yes
3 Yes, but I submit base64-encoded programs
1..3: 2
$ cargo compete login atcoder
```

AtCoderで使用されるRustのバージョンは1.42.0なのでtoolchainを入れる。
```sh
$ rustup toolchain install 1.42.0
```

必須ではないが、templateを自分好みにいじる。

```toml
[template]
src = '''
use proconio::{fastout, input};

#[fastout]
fn main() {
    input! {
    }
    todo!()
}
'''
```

### 問題&テストの取得と提出

`new`でディレクトリを生成する。
引数はコンテストのURLの末尾(ex. abc230, abs, typical90)
`--open`をつけると問題文をブラウザで開く。


```sh
$ cargo compete new abs
$ cd abs
```

コードを書いたら`submit`でテストと提出

テストが失敗した場合提出は行われない。

```sh
$ cargo compete submit abc086a
```

## 感想

割と楽。

かつ快適。

CLIはいいぞ。