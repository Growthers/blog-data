---
title: "Arduino boot loaderのお手軽書き込み方法"
date: 2022-03-15
---

Atmegaを奪われたArduino UNOが机の上に転がっていたり、ブートローダが吹き飛んだAtmega328pがあったりしますよね。今回は一番楽に(たぶん)Arduino UNOにブートローダを書き込む方法を紹介します。  


## 用意するもの
- ブートローダ がすでに書き込まれているArduino UNO(書き込み装置として使用)
- Arduino UNO(bootloader なし)
- ジャンパ線(6本ぐらい)
- USB TYPE-B 2.0 ケーブル(書き込み装置用)

## 書き込み装置の作成  

まず、Arduino UNOをAtmegaライターとして利用するための準備を行います。  
スケッチ例の中からArduinoISPを開いて、書き込み用のArduino UNOに書き込みます。  
`ファイル >> スケッチ例 >> 11.ArduinoISP >> ArduinoISP`  
![ArduinoISP](https://imgur.com/xlMQ3V6.jpg)

## ピンの接続

下記の表のSignalの通りに書き込み用Arduino UNOと書き込まれるArduino UNOをつなげてください。  


### 対応するピン    
Arduino UNO(書き込み装置)  

| Pin | Signal |                                    |
| --- | ---    | ---                                |
| GND | GND    |                                    |
| 5V  | VCC    |                                    |
| 10  | RESET  | ターゲットのマイコンをリセットする |
| 11  | MOSI   |                                    |
| 12  | MISO   |                                    |
| 13  | SCK    |                                    |

Arduino UNO(書き込まれる側)  

| Pin   | Signal |
| ---   | ---    |
| GND   | GND    |
| 5V    | VCC    |
| RESET | RESET  |
| 11    | MOSI   |
| 12    | MISO   |
| 13    | SCK    |

## 書き込み

`※書き込み装置が5V、ターゲットが3.3Vの組み合わせで書き込んではいけません` 壊れます。


この方法ではArduinoISPを利用している為、ツールの欄にある書き込み装置の項をArduino as ISPに変更してください。  
![SelectArduinoISP](https://imgur.com/Q0iK752.jpg)  

ボードとシリアルポートを正しく設定して、ブートローダに書き込むをクリックしてください。これでブートローダの書き込みは完了です。  
![WriteBootloader](https://imgur.com/l6ATgsw.jpg)  

## 解説
なぜ、この方法で書き込めるかの解説です。一般的なことしか書いていませんが不備があれば指摘をお願いします。  

そもそも、Arduino UNOはUSBの書き込み機能とI/Oを備えたAtmega328P用基板です。そのため、ISPを使う際に使用するピンに正しくつなげてしまえば書き込みすることができます。  ISP用のピンヘッダをつかって書き込むと少々めんどくさかったりするので、使えるときは楽をしましょう。

![PinMapping.jpg](https://imgur.com/UuAPJtQ.jpg)
[ATmega168/328P-Arduino Pin Mapping](https://docs.arduino.cc/hacking/hardware/PinMapping168?isogp=false)より引用

