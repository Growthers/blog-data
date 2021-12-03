---
title: "Markdown-itの拡張を作成する方法"
date: 2021-12-03
---

Markdown-itはjavascriptで実装されたMardownパーサーです。  
この記事ではMarkdown-itに独自記法を追加する方法を紹介します。  

完全な情報ではありません。markdown-itの構造を理解する助けにしていただく程度に活用いただけると幸いです。

初めにmarkdown-itの基本的な使用方法を記載します。  
npmからmarkdown-itパッケージをインストールしたのちnodejsから利用するか、esm.shやjspmをdenoやブラウザーで使えるようにしてください。  
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

markdown-itはレンダリングのためにstring型のデータ(markdown文書)を受け取りstring型のhtmlを返却します。  
受け取った文字列はまずパーサーに渡され、辞書を要素に持つ配列であるトークンに分割します。  
分割されたトークンはルールに従って文字列を返す関数でstring型のHTMLに変換されます。  

## parser  
markdownの文書は大きく「block」と「inline」に分けることができます。  
(inlineはブロックの一種である"inline" blockでパースされます。)  
空行で区切られた段落や見出し、コードブロックなどは「ブロック」として扱われます。  
また、太字や斜体、打ち消し線などの段落の中に収まる要素などはinlineとして扱われます。  

それぞれをパースし、トークンに分割する関数はMarkdownItインスタンスの「MarkdownIt.prototype.parse()」として定義されています。  
(上記に記載したブラウザ用サンプルコードではレンダラーのインスタンスをwindowに公開しているため、consoleから実際の関数を直接覗き見ることができます。)  
トークンの配列はブロックごとに区切られ、インライン要素はトークンのchildrenに記録されます。  
[実際にトークンがブロックごとにパースされる部分](https://github.com/markdown-it/markdown-it/blob/master/lib/parser_core.js#L45-L53)
```javascript
Core.prototype.process = function (state) {
  var i, l, rules;

  rules = this.ruler.getRules('');

  for (i = 0, l = rules.length; i < l; i++) {
    rules[i](state);
  }
};
```

つまり、新しい文法を定義したい場合はパーサーに新しく関数を定義する必要があります。  

実際のトークンを確認する:  
markdown-it公式のdemoにはデバッグタブが用意されており、入力に対するトークンの様子を確認できます。  

## ルーラー  
パーサーから渡されたトークン列を実際のHTML文字列に変換するのがルーラーの仕事です。  
基本的に、トークン一つに対応する関数がトークンを渡されて呼び出されます。  

※ イメージ  
```
"heading_open" => "<h2>"
"heading_close" => "</h2>"
```

ここまでMarkdown-itの動作を説明したのでプラグインの作成方法の解説をします。  
下記はMarkdown-itで外部からプラグインを読み込み適用する例です。  
実際に実行するとわかりますが、:+1:といった絵文字記法がレンダリングされるようになります。  

```javascript
import MarkdownIt from "http://esm.sh/markdown-it?bundle"
import MarkdownItEmoji from "http://esm.sh/markdown-it-emoji?bundle"

const md = new MarkdownIt()
md.use(MarkdownItEmoji)
```

md.use()に渡された関数はMarkdown-itのインスタンスを引数に渡され、実行されます。  
例として下記のような関数を作成し、md.use()に渡してみましょう。  

```javascript
import MarkdownIt from "http://esm.sh/markdown-it?bundle"

function plugin(md) {
  console.log(md)
}

const md = MarkdownIt()
md.use(plugin)
```

コンソールを確認すると、インスタンスの中身が出力されていることがわかります。  
プラグインはこの`md.use()`に渡された関数のなかで、パーサー関数やルーラーをインスタンスに登録します。  

この記事では私の作成したGithubライクなYamlヘッダの表示プラグインを紹介します。
[https://github.com/kat0h/markdown-it-meta-header](https://github.com/kat0h/markdown-it-meta-header)
![](https://user-images.githubusercontent.com/45391880/141127221-b483acd7-8fad-461f-b943-58a419ff5901.png)

parser:  
パーサ関数には次の3つの引数を取り、真偽値を返す関数が期待されます。  
`(state, startLine, endLine, silent) => boolean`

これらの情報は文書の全て(`state.src`)などの状態と、現在読んでいる位置などの情報です。  
パースしたら、読んでいる行を示すカウンターを増やして別の文法をパースする関数に処理を引き継ぎます。  
また、パースしたでーたはトークン列の配列に格納します。  
渡された文書の位置が自分のパースする文法ではない場合、falseを返してください。  

実際にパースをしているコードは下記の通りです。
[https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L7-L46](https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L7-L46)
このパーサ部分は[markdown-it-meta](https://github.com/CaliStyle/markdown-it-meta)を基にしています。  

`state.src`や`state.line`から本文をパースし`state.push()`からトークン列にトークンを追加してください。  
[https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L40-L45](https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L40-L45)
```
// 新しいトークンをトークン列に追加(rendererにはmetaを使用する)
var token = state.push('meta', '', 0)
// 読み取ったYAML(辞書)を記録
token.content = yaml
// 元の行
token.map = [startLine, state.line]
token.children = [];
// パースした行の次
state.line = line + 1
return true
```

renderer:  
rendererは受け取ったトークン列とidxから必要な情報を取得し、stringのHTMLを返す関数です。  
parserでpushしたトークンは`tokens[idx]`で取得できます。


md.use()で読み込めるようにする:  
プラグインはmd.use()に渡すことで利用できるようにすることが望ましいです。  

[https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L106-L109](https://github.com/kat0h/markdown-it-meta-header/blob/main/index.js#L106-L109)
```
function MarkdownItMetaHeader(md) {
  md.block.ruler.before('code', 'meta', meta_parse)
  md.renderer.rules.meta = meta_render
}
```

`md.block.ruler.before()`は特定のblockのパース前に実行するようにパーサーを登録します。
> なぜbefore()で登録する必要がある場合があるのか
> 今回作成したプラグインはMarkdownの`---`記法とバッティングします。  
> `---`記法が読み込まれる前にパース処理を挟むことができます。
> 詳細：[https://markdown-it.github.io/markdown-it/#Ruler](https://markdown-it.github.io/markdown-it/#Ruler)

`md.renderer.rules`にはrenderer関数を登録してください。これはparser関数で`state.push()`を実行する際の第一引数と同じキーを指定します。


以上で作成したプラグインを`md.use()`から読み込めるようになりました！
```
md.use(MarkdownItMetaHeader)
```

