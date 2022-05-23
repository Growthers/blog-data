---
title: "wpa_supplicantを入れ忘れたのでAlterでarch-chrootした話"
date: 2022-05-23
---

## まえがき
経緯を含めて説明するので対処法だけ知りたい人は**arch-chrootを使った**までどうぞ

## 経緯
帰省中に入れたUbuntu Serverを寮で使おうとして802.1x/radius用にwpa_supplicantを入れ忘れたことに気づいた愚かな人間がいるらしい <- 今ココ

## wpa_supplicantとは
クロスプラットホームなサプリカントでLinuxでWi-Fiに接続するときによく使われる

ArchWiki [wpa_supplicant](https://wiki.archlinux.jp/index.php/Wpa_supplicant)

## IEEE802.1x認証
ネットワークに接続するためにユーザー認証が必須な規格。某弊学で有線を使おうとすると必ず使う必要がある。  
昨年の春に突如として導入され、前情報もクソもなかった為、多くの寮生が「APEX出来ねえんだけど！？」とキレ散らかした。  
WindowsでやるよりもLinuxで接続したほうが、簡単説がある認証規格である。  

![tweet](https://imgur.com/BXEu8KQ.jpg)  
キレ散らかしていた様子  

## 対処１ 落としてきたdeb経由でインストール(失敗)
一度、wpa_supplicantをdebで落としてきて、インストールした経験があったので、実行した。尚、依存関係が壊れて失敗。

## 対処２ chroot経由でインストール(失敗)
Archlinux install battleでchrootを使った経験があったので、AlterLinux経由でchrootをするという方針を建てた。  
だいたい、あってたけど１つ問題があった。  

AlterLinuxを使うメリット  
802.1x認証の設定がGUIだとものすごく楽なので、おすすめ。  
ネットワークの設定あたりから設定可能。  

[AlterLinux](https://alter.fascode.net/)  
  
  
  

`/mnt` にUbuntu Serverの `/` に当たるパーティションをマウントして、chrootする。  

```sh
# mount /dev/sdax /mnt
# chroot /mnt
```

しかし、`/etc/resolv.conf`がコピーされない為、名前解決が失敗する。  
![discord_msg](https://i.imgur.com/YVUVUt3.png)  
  
  
## 対処３ arch-chroot経由でインストール(成功)
> `/usr/bin/chroot`を実行する前にこのスクリプトは `/proc`などの api ファイルシステムをマウントして、chroot から使える `/etc/resolv.conf` を作成します。
  
[chrootより引用](https://wiki.archlinux.jp/index.php/Chroot#arch-chroot_.E3.82.92.E4.BD.BF.E3.81.86?isogp=false)  
らしいです。  

```sh
# mount /dev/sdax /mnt
# arch-chroot /mnt
# apt install wpasupplicant
```

arch-chrootを使うことで名前解決ができるようになり、aptを使うことができる。  

## まとめ  
AlterLinuxでarch-chrootをすると、GUIで802.1x認証が出来て楽  
  
## 参考
[Archwiki wpa_supplicant](https://wiki.archlinux.jp/index.php/Wpa_supplicant)  
[ArchWiki chroot](https://wiki.archlinux.jp/index.php/Chroot)   
[IEE802.1X認証とは、EAPとは](https://www.infraexpert.com/study/wireless14.html)  
