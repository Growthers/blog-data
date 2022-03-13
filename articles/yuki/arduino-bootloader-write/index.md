----
title: "Arduino boot loaderのお手軽書き込み方法"
---

## 用意するもの
- SPI通信ができるArduino 互換機(書き込み装置として使用)
- Arduino UNO(bootloader なし)
- ジャンパ線(5本ぐらい)
- USBケーブル(書き込み装置用)

## 1. 書き込み装置の作成

Arduinoを書き込み装置として使用する場合、Arduino ISPをArduinoに書き込む

ソースコード

ファイル>スケッチ例>11.ArduinoISP>ArduinoISP

## 2. ピンの接続

Arduino UNO(書き込み装置)

| Pin | Signal |  |
| --- | --- | --- |
| GND | GND |  |
| 5V | VCC |  |
| 10 | RESET | ターゲットのマイコンをリセットする |
| 11 | MOSI |  |
| 12 | MISO |  |
| 13 | SCK |  |

Arduino UNO(書き込まれる側)

| Pin | Signal |
| --- | --- |
| GND | GND |
| 5V | VCC |
| RESET | RESET |
| 11 | MOSI |
| 12 | MISO |
| 13 | SCK |

## 3. 書き込み

1. 書き込み装置の選択
    
ツール>書き込み装置>Arduino as ISP
    
2. ブートローダの書き込み

`※書き込み装置が5V、ターゲットが3.3Vの組み合わせで書き込んではいけない` 壊れます
