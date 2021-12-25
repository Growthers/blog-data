---
title: "美っち生イき は良いぞ！ あと、C言語のUnicodeの話"
date: 2021-12-25
---

この記事は[共同開発鯖 Advent Calendar 2021](https://qiita.com/advent-calendar/2021/growthers) 25日目の記事です。
## 美少女になってちやほやされて人生イージーモードで生きたい！ は良いぞ
サンタさんに「美少女になってちやほやされて人生イージーモードで生きたい！」と願ってもTS転生できませんでした。悲しいね

さて、ここ１年ぐらい推している作品を紹介します。  
[美少女になってちやほやされて人生イージーモードで生きたい！](https://syosetu.org/novel/217501/)  
作: [紅葉煉瓦](https://twitter.com/akaba_renga?isogp=false)  

この作品は、「TS転生した主人公が新進気鋭のVtuberグループに所属して、Vtuberとして活動していく」というストーリーが主軸となっている作品です。
主人公が見ていて飽きないので良いぞ

この作品、ストーリーの良さはさることながら、ハーメルンの特殊タグの使い方がすごいです。
具体的に言うと、特殊タグだけで、YouTubeの動画再生画面の再現などが行われています。

個人的にCSSが死ぬほど苦手なので、そういった面でも尊敬している作品です。
小説版を読むのに抵抗がある場合は[大生月](https://twitter.com/NmnmOtk)先生の手によってコミカライズされた[漫画版](https://seiga.nicovideo.jp/comic/56102?track=verticalwatch_epinfo1)をどうぞ

こよいちゃんがわちゃわちゃしていてめちゃくちゃかわいいので是非読んでください。


## 技術的な小話
こんな布教しかない記事がアドカレの最終日の記事だと殺されかねないので、仕方ないですが技術的な話をします。  

RustやTypeScriptでUTF-8の変数が使えるという話は有名ですが、C言語でもUnicodeが使えるという話をご存知でしょうか？  


例えばこのコードはClangやGCC 10以降ならコンパイルすることができます。  

```c
int main(void){
   char* ほげ = "ほげ";
   printf("%s\n",ほげ);
}
```

C言語では`\U0001F385`のようにUnicodeをエスケープすることで識別子として使用できます。  
また、コンパイラ依存ですが、エスケープしていない文字を使うこともできます。  
  
  
  

```c
int main(void) {
  char *🎅 = "Merry Christmas";
  printf("%s\n", \U0001F385);
}
```  


Unicodeが使えることが分かったら、コンパイルが通る範囲で遊んでみたくなりますよね？  
ということで、1ヶ月ほど前に生えたアホなコードを紹介していきます。  
  
```c
#include <stdbool.h>
#define たいぷでふ typedef
#define もし if
#define りたーん return
#define めいん main

たいぷでふ int いんと;
たいぷでふ void ぼいど;

いんと さいき(いんと ひきすう) {
  もし(ひきすう == 1) りたーん 1;
  りたーん ひきすう *さいき(ひきすう - 1);
}

ぼいど ぷりんとえふ(いんと ひょうじするやつ) {
  printf("%d\n", ひょうじするやつ);
}

いんと めいん(ぼいど) {
  ぷりんとえふ(さいき(10));
  りたーん 0;
}
```
  
このコードはClangもしくはGCC10以上でコンパイルすることができます。

こちらのコード、C言語で(処理系依存だけれど)日本語が使えるという話を聞き、知能指数をいい感じに下げながら書いたものです。  
これの前身となったコードを[Twitter](https://twitter.com/yuki_yuigishi/status/1463394403683160067?s=20)に貼ったら謎にウケました。


```c
#define ㅤㅤ printf
#define ㅤㅤㅤ main
#define ㅤㅤㅤㅤ char*
ㅤㅤㅤ() {
  ㅤㅤㅤㅤ ㅤ = "TEST!";
  ㅤㅤ("%s\n",ㅤ);
}
```
[@kat0h 2021/11/25 22:38 のツイートより引用  ](https://twitter.com/kat0h/status/1463864756654071816?isogp=false)  
このコードはClangでのみコンパイルすることができます。  

こちらは@kat0hさんが書いた空白を変数名につかったコードです。 [ここ](https://paiza.io/projects/9Mjar0LlF2FMnU_4_Xzf_Q)から実行できます。  

このコードでは空白文字のHANGUL FILLER(U+3164)を変数名として使用しています。  
このコードをコンパイルすると  
```bash
$ clang test.c

test.c:4:1: warning: type specifier missing, defaults to 'int' [-Wimplicit-int]
<U+3164><U+3164><U+3164>() {
^
test.c:2:19: note: expanded from macro 'ㅤㅤㅤ'
#define <U+3164><U+3164><U+3164> main
                                 ^
test.c:6:3: warning: implicitly declaring library function 'printf' with type 'int (const char *, ...)' [-Wimplicit-function-declaration]
  <U+3164><U+3164>("%s\n",<U+3164>);
  ^
test.c:1:16: note: expanded from macro 'ㅤㅤ'
#define <U+3164><U+3164> printf
                         ^
test.c:6:3: note: include the header <stdio.h> or explicitly provide a declaration for 'printf'
test.c:1:16: note: expanded from macro 'ㅤㅤ'
#define <U+3164><U+3164> printf
                         ^
2 warnings generated.
```
  
こんな感じでU+3164を変数名として使っていることがわかります。  

## どの文字が識別子として使用できるのか  
C言語でUnicodeが使用できるのはC言語の仕様として制定されているからです(尚、エスケープされていない文字の使用は処理系依存)。  
では、逆にどの文字が使えないのでしょうか？  

ISO/IEC 9899:2011のドラフトを参照してみると  
> **Constraints**
> 2 A universal character name shall not specify a character whose short identifier is less than
> 00A0 other than 0024 ($), 0040 (@), or 0060 (‘), nor one in the range D800 through
> DFFF inclusive.73)
> **Description**
> 3 Universal character names may be used in identifiers, character constants, and string
> literals to designate characters that are not in the basic character set.
> **Semantics**
> 4 The universal character name \Unnnnnnnn designates the character whose eight-digit
> short identifier (as specified by ISO/IEC 10646) is nnnnnnnn.
> 74) Similarly, the universal
> character name \unnnn designates the character whose four-digit short identifier is nnnn
> (and whose eight-digit short identifier is 0000nnnn).  

「[ISO/IEC 9899:2011 N1570](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf?isogp=false)  6.4.3 Universal character nemes」より引用  


> **制約事項**
> 汎用文字名は，短識別子が 0024 ($), 0040 (@), 0060 (') 以外の 00A0 未満の文字及び D800 から DFFFを含む73) 。
> **説明**
> 汎用文字名は、識別子、文字定数、文字列リテラルの中で、基本文字セットに含まれない文字を指定するために使用することができます。
> **セマンティクス**
> 世界共通文字名称 \Unnnnnn は、ISO/IEC 10646 で規定される 8 桁の短い識別子を nnnnnn とする文字を指します。
>  同様に、4桁の短縮識別子がnnnn（8桁の短縮識別子が0000nnnn）である文字を、Universal Character Name \unnnn で指定します。

DeepLによって翻訳

U+0024 ($), U+0040 (@), U+0060 (') 以外の U+00A0 未満の文字とU+D800 から U+DFFFまでは使えないようです。

U+00A0未満のUnicodeは[基本ラテン文字 (Unicodeのブロック)](https://ja.wikipedia.org/wiki/%E5%9F%BA%E6%9C%AC%E3%83%A9%E3%83%86%E3%83%B3%E6%96%87%E5%AD%97_(Unicode%E3%81%AE%E3%83%96%E3%83%AD%E3%83%83%E3%82%AF?isogp=false))によると、ASCIIコードで表現できる文字にあたるそうです。
また、U+D800からU+DFFFはWikipediaの[Unicode](https://ja.wikipedia.org/wiki/Unicode#%E3%82%B5%E3%83%AD%E3%82%B2%E3%83%BC%E3%83%88%E3%83%9A%E3%82%A2?isogp=false)によるとサロゲートペアにあたるそうです。

つまり
```c
int main(void){
   int \u00000041 = 0;
   A = 1;
}
```
みたいなコードはコンパイルエラーになります。

## 終わりに
今回は、C言語でUnicodeを使用する際の注意点などをまとめてみました。
Cの規格書を探すのに少々苦労しましたが、使えるUnicodeの範囲を知ることができました。
C言語でUnicodeを識別子として使用するという記事はあまりなかったので、誰かの参考になれば幸いです。


