---
title: "Gitのブランチ名をdelete/hogeにするな"
date: 2022-01-08
---
## 新年の挨拶

明けましておめでとうございます(もう正月終わったぞ？
今年はGrowthers全体で週１更新を目指してがんばります

## Gitのブランチ名をdelete/hogeするな

Oshaveryの開発中にファイルを大量に削除する機会があり、`delete/files`というブランチ名でブランチを切り作業を行いました。  
ローカルでブランチを切って作業を行い、GitHubへpushするところまではよかったのですが、 PullRequestの際に、delete/filesブランチにアクセスすると  

![github404.jpg](https://imgur.com/Ih8mrHy.jpg)

という風にhttps://github.com/404 へリダイレクトされてしまいました。  
  
ほぼ全ファイルを削除するというPRだったので、流石に不味いと思いブランチ名を`remove/files`にすることでGot a kotonakiとなりました。  

## 検証  
とりあえず、HTTPメソッドの中からPOST , GET , PUT , DELETE の4つをブランチ名に付けて検証してみます。  
ローカルでpost/testのようにブランチを切り、GitHubにpushして検証しました。  

![github_branches.jpg](https://imgur.com/6EU2zNP.jpg)

| ブランチ名  | 結果 |
| ----------  | ---- |
| POST/test   | ×    |
| GET/test    | ×    |
| PUT/test    | ×    |
| DELETE/test | ×    |
| post/test   | ×    |
| get/test    | ×    |
| put/test    | ×    |
| delete/test | 〇   |


追加検証として、GitLabに`delete/test`という名前のブランチを生やしてアクセスしてみたところ、普通にアクセスできました。


## まとめ

検証結果からは何もわかりませんでしたが、[Qiitaの元@500さん](https://qiita.com/nginx/items/e9afcdd7374c9eaeb472?isogp=flase)の様にルーティング周りのバグを踏んでしまった可能性が高いことがわかりました。  
今後は`delete/files`の様なブランチ名をつけるのはやめましょう。  
