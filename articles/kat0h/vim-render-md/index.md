---
title: "Vimでmarkdownをプレビューするプラグインを作った"
date: 2021-12-06
---

VimでMarkdownをプレビューするプラグインを作成しました(この記事は9月にZennに投稿した記事と同じテーマです)
![](https://user-images.githubusercontent.com/45391880/134791644-5f69ee3e-a6ab-4d24-878b-7131dc9a3f4c.gif)

9月頃から[bufpreview.vim](https://github.com/kat0h/bufpreview.vim)というVim向けのプラグインを作成していました。
Vim向けのMarkdownプレビュープラグインは過去にも存在していましたが、このプラグインは下記のような特徴をもっています。

- Typescriptで記述されたVimとは独立して動作する高速なサーバー
- nodejs製プラグインで必要だった依存関係のインストールが不要
- Github風のMarkdownレンダリング(yamlヘッダのレンダリングも含めて)
- 入力の即時反映
- カーソル位置とブラウザーの表示位置の同期

この記事はそれぞれの特徴について解説します。

## Typescript製サーバー
このプラグインは[denops.vim](https://github.com/vim-denops/denops.vim)を利用してTypescript実行環境であるDenoとVimを繋げ、通信しています。
Websocketでブラウザー間とJsonをやり取りしているので、Vimの状態を即座に反映することができます。

## nodejs製プラグインで必要だった依存関係のインストールが不要
この特徴は[https://github.com/iamcco/markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)にあるものです。

bufpreview.vimはmarkdown-preview.nvimの挙動を参考にして作成しました。非常に使い心地の良いプラグインでお勧めなのですが、nodejs製のプラグインであるため起動前に依存関係をインストールする必要があります。
依存関係の解消に(多少)手間がかかることは気軽に使うにあたって問題になることがあるかもしれません。

bufpreview.vimではサーバにDenoを使用しているため、依存関係が自動でインストールされます。

## Github風Markdownレンダリング
Markdownの仕様は核レンダラーごとに様々であり、混沌とした状況になっています。
このプラグインではレンダラーにjavascript製Markdownレンダラーの[markdown-it](https://github.com/markdown-it/markdown-it)を利用し、各種プラグインを取り入れおおまかにGFMを再現するようにしています。

また、Markdownの先頭にyaml形式の情報を付けるyamlヘッダのレンダリングにも対応しています。これは他のMarkdownレンダラにはない特徴です。
この機能を実現するために[markdown-it-yaml-header](https://github.com/kat0h/markdown-it-meta-header)というmarkdown-itプラグインを作成しました。(まだmainで利用していませんが。。)

### esm.sh
このプラグインではMarkdownのレンダリングをブラウザーで行なっています。普通nodejs向けに作られたライブラリはそのままブラウザーで読み込むことができません。
そこで、esm.shというnpmに公開されているモジュールをESModuleとして変換してくれるCDNを利用しています。これを利用することでブラウザーから下記の例のようにnpmが提供するライブラリを読み込めます。

```html
<!DOCTYPE html><html>
<head>
  <meta charset="utf-8" />
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/github-markdown-css@4.0.0/github-markdown.min.css">
</head>
<body>
  <textarea id="text" style="height: 162px; width: 250px"></textarea><br>
  <button type="button" onclick="render()" label="Render">
    Render
  </button><br>
  <div class="markdown-body" id="body">
  </div><br>
  <script type='module'>
    import MarkdownIt from "https://esm.sh/markdown-it?bundle"
    const md = new MarkdownIt();
    const render = () => {
      var text = document.getElementById("text").value;
      text = text == null ? "" : text;
      const body = document.getElementById("body");
      body.innerHTML = md.render(text);
    }
    window.render = render;
    window.md = md;
    render();
  </script>
</body>
</html>
```

ブラウザーからESModuleを利用するには`<script type='module'></script>`としてjavascriptを記述する必要があります。また、moduleとして記述したjavascriptはstrictモードで読み込まれます。注意してください。(一敗)

ESModuleを利用することで、HTML一枚のみで様々なライブラリを管理しやすく実装することができました。(この仕組みがなければ、ここまでシンプルに依存関係を管理できませんでした)

## 入力の即時反映
Vimとサーバーをwebsocketで常時接続しているため、Vimへの入力はブラウザーへすぐに反映されます。

## カーソル位置とブラウザーの表示位置の同期
markdown-itのプラグインである、[https://github.com/digitalmoksha/markdown-it-inject-linenumbers](https://github.com/digitalmoksha/markdown-it-inject-linenumbers)を利用して入力行とレンダリングされた結果を対応させています。

## 今後
現状bufpreview.vimはmarkdown-itを利用したmarkdownのレンダリングのみにした対応していません。
現在任意のレンダラーを追加して利用できる仕組みを実装しようとしているのですが、仕様を考えるのが難しく未だ実装できていません。

任意のレンダラーに対応できるようになれば
- Markdown -> スライド
- 小説
- markdown-it以外のレンダリングエンジンに変更
などかなり柔軟に使えるようになると考えています。


