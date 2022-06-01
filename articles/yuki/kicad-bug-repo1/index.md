---
title: "KiCad6のPCB Editorでフリーズする原因はIMEかもしれない"
date: 2022-06-1
---

定期テスト駆動開発をKiCad6でやろうとしたら、結構クリティカルなバグに引っ掛かったので共有しておきます。

## バグの内容(2021/6/1時点)
対象ののバージョン KiCad6  
対象IME            Microsoft IME  
  
## 内容  
IMEを半角にした状態でPCB Editor(旧Pcbnew)で任意のキーを入力すると、KiCad全体がフリーズする。  
  
## 原因  
IME関連のバグ  

## 該当のissue  
https://gitlab.com/kicad/code/kicad/-/issues/9882  
KiCadマネージーからではなく、PCB Editor単体で起動すると問題は発生しないらしい。(確認済み)  


## 対処法  
Microsoft IMEの互換性を有効化する。  
[IMEの互換性を有効にする方法(他サイト)](https://pc-karuma.net/windows-10-previous-version-microsoft-ime/)


Windows11やGoogle 日本語入力では問題が発生しないという報告もあるようです。  
修正にはもう少し時間がかかりそうな様子なので、IMEの互換性の有効化をおすすめします。  

## 参考
[Microsoft Pinyin causes KiCad freezing](https://gitlab.com/kicad/code/kicad/-/issues/9882?isogp=false)  
[Windows10 – 以前のバージョンの Microsoft IME を使う](https://pc-karuma.net/windows-10-previous-version-microsoft-ime/?isogp=false)  
