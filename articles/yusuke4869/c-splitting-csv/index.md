---
title: "C言語でcsv分割"
date: 2021-12-20
---

この記事は[共同開発鯖 Advent Calender 2021](https://qiita.com/advent-calendar/2021/growthers)の20日目の記事です.  

## はじめに  

先日, C言語でcsvのデータを分割するコードを書く機会があり, かなり手こずったので振り返りつつ書いていきたいと思います.  

### データ  

この記事で使用するcsvデータは以下のものとなります.  
```csv
goods,cost
pen,100
"pencil, eraser",200
ruler,110
```

## 方針  

初め, 以下の方針で文字区切りを行おうと考えていました.  

1\. `strtok`関数を使用して, `NULL`が返るまで`,`で区切る  
2\. 返ってきたデータを順番に構造体に入れる  
3\. 各行に対して1, 2を繰り返す  

1, 2に相当するコードは以下のようになります.
```c
#include <stdio.h>
#include <string.h>
/*
略
*/
char *tmpstr, str[256] = "goods,cost";
tmpstr = strtok(str, ",");
// , で区切り
while (tmpstr != NULL) {
    printf("%s\n", tmpstr);
    tmpstr = strtok(NULL, ",");
}
/*
略
*/
```

各行のデータを`str`に入れて実行すると以下のようになりました.  
```
goods
cost
pen
100
"pencil
 eraser"
200
ruler
110
```

以下のように出力してほしいのですが, `"pencil, eraser"`の中の`,`でも区切られてしまいうまくいきません.  
```
goods
cost
pen
100
pencil, eraser
200
ruler
110
```

ということで, `strtok`と同じような挙動をしつつも, `"`に囲まれた`,`は無視する関数を作成することにしました.  

## 関数の作成  

今回の関数は以下を満たす必要があります.  
- 区切り文字は`,`であること
- `"`で囲まれた区切り文字(`,`)は無視すること
- 区切った箇所から前の値は返し, 残りの値は保持すること
- `"`は返り値から除外する

`"pencil, eraser"`のような部分があったときに, `"`の部分は返す必要がなかったので, `"`を返り値から除外するということも盛り込みました.  

そうして作成した関数が以下です.  
```c
void csvsplit(char *result, char target[]) {
    static char savedstr[256];
    char delim = ',';
    char tmp[256], result_tmp[256], tail_tmp[256];
    target = target[0] != '\0' ? target : savedstr;

    if (target[0] == '\0') {
        strcpy(savedstr, "\0");
        strcpy(result, target);
        return;
    }

    strcpy(tmp, target);

    int isCut = 0, isContent = 0;
    for (int i = 0, result_index = 0, tail_index = 0; i < 256; i++) {
        // 末尾かどうか
        if (tmp[i] == '\0') {
            tail_tmp[tail_index] = '\0';
            if (!isCut) result_tmp[result_index] = '\0';
            break;
        }

        // 区切り後かどうか
        if (isCut) {
            tail_tmp[tail_index] = tmp[i];
            tail_index++;
            continue;
        }

        // ダブルクォーテーションかどうか
        if (tmp[i] == 0x22) {
            isContent = (isContent == 0) ? 1 : 0;
            continue;
        }

        // 区切られておらず, 区切り文字かどうか
        if (tmp[i] == delim && !isContent) {
            result_tmp[result_index] = '\0';
            isCut = 1;
            continue;
        }

        result_tmp[result_index] = tmp[i];
        result_index++;
    }

    strcpy(result, result_tmp);
    strcpy(savedstr,tail_tmp);
}
```

仮引数の`result`は結果を代入する変数を, `target`は区切る文字列を取るようになっています.  

そこから`tmp`を一文字ずつ確認し, `,`や`"`があるかどうか, 区切ることが可能な位置か(`isContent`), すでに区切りは完了しているか(`isCut`)などを変数使用するなどして管理するようにしました.  
返り値の部分は`result`に, 保持する部分は`savedstr`に代入して終了となっています.  

`savedstr`を区切る文字列にする場合, 仮引数の`target`には`"\0"`を代入します.  

Sayoiくんに一部修正してもらいました. ありがとうございます.  

## 挙動の確認  

作成した関数を使用して動作確認をしていきます.  

```c
#include <stdio.h>
#include <string.h>
void csvsplit(char *, char[]);
/*
略
*/
char tmpstr[256], str[256] = "goods,cost";
csvsplit(tmpstr, str);
// , で区切り
while (tmpstr[0] != '\0') {
    printf("%s\n", tmpstr);
    csvsplit(tmpstr, "\0");
}
/*
略
*/
```

各行のデータを`str`に入れて実行すると以下のようになりました.  
```
goods
cost
pen
100
pencil, eraser
200
ruler
110
```

これで無事, csvの分割を行うことができました.  
