---
title: "Armbian Build Battle"
date: 2021-12-08
---

 Armbian Build Battle
この記事は共同開発鯖 Advent Calendar 2021 8日目の記事です
https://qiita.com/advent-calendar/2021/growthers

## はじめに

どうも雪です。アドカレのネタが思いつかず、ギリギリになってしまいました。
今回はPine A64に対応しているディストリビューションの１つArmbianのビルドをやってみたいと思います。最初に謝っておきますが、[アホ](https://twitter.com/yuigishidev)がMicro Bをなくしてしまったせいで動作確認ができていません。MicroBを見つけられたら後日追記します。


## Pine A64とは
Pine64という[PinePhone](https://www.pine64.org/pinephone/)や[PineTime](https://www.pine64.org/pinetime/)などのギーク向けのLinuxが動く製品数多く販売している企業の販売するシングルボードコンピュータの一つです。
### Pine A64とRaspberry Pi 3 Model Bの比較


|          | Pine A64+ | Raspberry Pi3 Model B |
| -------- | -------- | -------- |
| SoC     |1.152GHz 4 x Coretex A53|1.2GHz 64bit 4 x Coretex A53 |
| RAM     | 1GB 〜 2GB | 1GB |
|Ethernet | 1000BASE-T| 100BASE-T |
|映像出力  | HDMI | HDMI |
|Bluetooth| 技適未取得のため日本国内では使用不可| Bluetooth v4.1 BLE|
|無線LAN| 技適未取得のため日本国内では使用不可 |  IEEE802.11b/g/n (2.4GHz)|
|値段    | 1GB 2980円 / 2GB 4480円 | 4950円 〜|  

ラズパイ3 Model Bと似たスペックでラズパイ3 Model Bよりも安く買うことができます。
現在は[Pine A64+](https://www.pine64.org/devices/single-board-computers/pine-a64/)の後継機[Pine A64-LTS](https://www.pine64.org/devices/single-board-computers/pine-a64-lts/)のみの販売になってしまったようですが、秋月電子通商などではまだ在庫が残っているようなので、興味のある方は買ってみてください。  
https://akizukidenshi.com/catalog/g/gM-12067

## Armbianとは  
> Armbian is a base operating system platform for single board computers (SBCs) that other projects can trust to build upon.  
>  
> - Lightweight Debian or Ubuntu based Linux distribution specialized for ARM development boards  
> - Each system is compiled, assembled and optimized by Armbian Build Tools  
> - It has powerful build and software development tools to make custom builds  
> - A vibrant community  
> 
Armbian Documentation [What is Armbian](https://docs.armbian.com/)より引用  

DebianやUbuntuなどの有名ディストリビューションがサポートしていないシングルボードコンピュータをサポートすることを目標としているビルドフレームワークらしいです。
シングルボードコンピュータ向けのイメージをかなり簡単にビルドできます。

## 実行環境
OS: Ubuntu 20.04.3 LTS x86_64
CPU: Ryzen 5 3600
Memory: 16GB
Shell: fish shell

## ビルド方法

### Armbian Build Toolsの入手
まず最初に、Armbian Build Toolsを入手します。
```
$ git clone --depth 1 https://github.com/armbian/build
$ cd build
$ ./compile.sh docker
```
locale関係のエラーでビルドが落ちまくったので、今回はdockerを使ってビルドします。
(たぶんjp_JP.UTF-8が存在しないせいだと思います)

https://github.com/armbian/build

たったこれだけで、ビルドツールが起動します。

![](https://i.imgur.com/964E8NQ.png)


Full OS image for flashingを選択して
今回はkernelをいじらないのでDo not change the kernel configurationを選択してください  


![](https://i.imgur.com/82sC9Al.png)
ボードの選択メニューが出てくるので、ビルドしたい対象を選んでください(今回はpine64)

![](https://i.imgur.com/5D6G79g.png)

current か legacyのどちらかを選択し、好きなディストリビューションを選択します。

あとは好きなように設定してください。　　

ビルド時間は30分ぐらいかかったので、気長に待ちましょう。

**検証ができているのはここまでです。この方法でビルドしたイメージが実際に起動できるかは保証できません**  

## 感想
思ったより簡単にビルドすることができて驚きました。
MicroBが見つからず、検証までたどり着けなかったのは残念ですが、見つかり次第検証してみたいと思います。

## 参考
[Armbian Documentation](https://docs.armbian.com/)
