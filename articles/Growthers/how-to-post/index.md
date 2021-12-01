---
title: Blogの書き方
date: 2021-12-01
---

この記事は弊鯖民に向けた記事です.  
[共同開発鯖 Advent Calender 2021](https://qiita.com/advent-calendar/2021/growthers)の4日目の記事でもあります.  

## 最初に  

ブログのデータはすべて[ここ](https://github.com/Growthers/blog-data)で管理されています.  
作業をするにあたって, このリポジトリをForkしてください.  

## ユーザーの作成  

記事を初めて書く場合, ユーザーを作成する必要があります.  
`hoge`というユーザーを作成する場合, `articles/`に`hoge`というディレクトリを作成してください.  

#### ディレクトリ名を作成するにあたって  

このディレクトリ名はURLのパスなどにも使用されます.  
**なるべく変更せず, 半角英数字を使用してください.**  

### `profile.json`の作成  

以下の形式に従って, `profile.json`というファイルを`articles/hoge`に作成してください.  

```json
{
    "name": "Your Display Name",
    "icon": "Your Icon URL",
    "bio": "Your Biography",
    "site": "Your Website URL",
    "github": "Your GitHub ID",
    "twitter": "Your Twitter ID",
    "roles": ["Your Role 1", "Your Role 2...", "Your Role N"]
}
```

|key|description|require|
|:-:|:-:|:-:|
|name|表示名|必須|
|icon|アイコンURL||
|bio|自己紹介||
|site|サイトURL|
|github|GitHub ID||
|twitter|Twitter ID||
|roles|役割（配列）||

## 記事の作成  

記事を作成する場合, `articles/hoge`に記事名のディレクトリを生成してください. (半角英数字を推奨)  
**ユーザー名と同じく, ディレクトリ名がそのままURLのパスになります.**  

その後, 生成したディレクトリに`index.md`というファイルを作成し, そこに記事を書いていきましょう.  
**`index.md`以外のファイル名を使用することはできません.**

`fuga`という記事を`hoge`というユーザーで作成した場合のディレクトリ構成
```
├ README.md
└ articles
    └ hoge
        ├ profile.json
        └ fuga
            └ index.md
```

### メタ情報の設定  

`index.md`の先頭にメタ情報を設定する必要があります.  

|key|description|require|
|:-:|:-:|:-:|
|title|記事の表示タイトル|必須|
|date|記事の作成日 `yyyy-mm-dd` の形式|必須

例: 
```
---
title: fuga
date: 2021-12-01
---
```

メタ情報は今後のブログのアップデートとともに増えていく予定です.  

#### 画像の挿入  

以下のいずれかの方法によって記事で画像を使用することができます.  

|方法|詳細|
|:-:|:-:|
|記事と同じディレクトリに保存する||
|外部に保存する|[imgur](https://imgur.com)など|

#### ツイートを掲載する  

ツイートを掲載したい場合, URLのみを貼り付けるのではなく[こちらのサイト](https://publish.twitter.com/)を使用して, 埋め込みコードを貼り付けてください.  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">弊鯖のアイコンを .<a href="https://twitter.com/771tenNext?ref_src=twsrc%5Etfw">@771tenNext</a> さんに書いていただきました✨<br>これからも弊鯖は色々やっていきます！<a href="https://twitter.com/hashtag/%E5%85%B1%E5%90%8C%E9%96%8B%E7%99%BA%E9%AF%96?src=hash&amp;ref_src=twsrc%5Etfw">#共同開発鯖</a> <a href="https://t.co/oEuvdeFf0J">https://t.co/oEuvdeFf0J</a></p>&mdash; 共同開発鯖 | Growthers (@UdcGrowthers) <a href="https://twitter.com/UdcGrowthers/status/1397186255490801667?ref_src=twsrc%5Etfw">May 25, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 記事の投稿  

記事を書き終えたら, 投稿をしましょう.  
最初にForkしたリポジトリにコミットをして, [blog-data](https://github.com/Growthers/blog-data)にプルリクエストを出します.  
2人以上のユーザーに承認され, マージされたら投稿完了です.  

## 最後に  

このブログへの記事の投稿方法を紹介しました.  
不明な点があれば, 気軽に質問をしてほしいと思います.  
