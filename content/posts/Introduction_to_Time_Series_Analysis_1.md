---
title: 時系列解析入門 1章
date: 2015-04-18T13:13:37+09:00
draft: false
tags: [統計, 時系列]
aliases:
    - /archives/122
---

北川先生の時系列解析入門の読書記録です。

## 1.1 時系列データ
時間の経過とともに不規則に変動する現象の記録が時系列

## 1.2 時系列の分類
### 連続時間時系列と離散時間時系列
 * 連続時間時系列
    * 例：アナログレコーダ
 * 離散時間時系列
    * 例：一時間おきに計測された気圧

### 一変量時系列と多変量時系列
 * 一変量
    * 観測時点で一種類の情報
 * 多変量
    * ２つ以上の情報 

### 定常時系列と非定常時系列
 * 時間的に変化しない一定の確率的モデルの実現値とみなせる
    * 定常時系列
 * 平均が時間とともに変動、平均のまわりの変動の仕方が時間的に変化
    * 非定常時系列

### ガウス型時系列と非ガウス型時系列
 * 時系列の分布が正規分布に従う
    * ガウス型時系列
 * 時系列の分布が正規分布に従わない
   * 非ガウス型時系列

非ガウス型時系列のように上下非対称性が見られても、データを変換することによって、近似的にガウス時系列とみなせる場合がある

### 線形時系列と非線型時系列
 * 線形なモデルの出力として表現できるような時系列
    * 線形時系列
 * 非線形なモデルが必要な時系列
    * 非線型時系列

### 欠測値と異常値
 * 何らかの理由で観測値が記録されない
    * 欠測値
 * 現象の異常な振る舞い・観測装置の異常など
    * 異常値

## 時系列解析の目的
### 記述
図示、標本自己共分散関数、標本自己相関関数、ピリオドグラムなど

### モデリング
 * 与えられた時系列の変動の仕方を表現する時系列モデルの構成
 * 時系列の確率的構造の解析

### 予測
時系列が互いに相関を持つことを利用して、現在までに得られた情報から今後の変動を予測すること

### 信号抽出
時系列から目的に応じて必要な信号や情報を取り出すこと

## 時系列データの前処理
非定常なデータを定常化する
### 変数変換
#### ロジット変換
$z_n = log \left( \frac{y_n}{1-y_n} \right)$
より一般的な方法として、Box-Cox変換がある
#### 差分（階差）
$z_n = \Delta y_n = y_n - y_{n-1}$
 
$y_n = a + bn$であれば、
$z_n = \Delta y_n = b$となり直線の傾きが除去できる


y_n = a + bn + cn^2$ならば、

\begin{align\*}
$\Delta z_n & = z_n - z_{n-1} \\\\
                   & = \Delta y_n - \Delta y_{n-1} \\\\
& = (b + 2cn) - (b +2c(n-1)) \\\\
& = 2c
\end{align\*}
となり2次成分及び１次成分を除去できる。

年周期であれば

\begin{align\*}
\Delta_p y_n = y_n = y_{n-p}
\end{align\*}
として一周期前の値との差分を利用することがある。

#### 前期比, 前年同期比
経済時系列などでは、減系列の前期比や前年同期比を分析することがある

$z_n = \frac{y_n}{y_n - 1}$

$x_n = \frac{y_n}{y_{n-p}}$

 * 時系列$y_n$が$y_n=T_n w_n$とトレンドとノイズの積
 * $T_n = (1+\alpha) T_{n-1}、$\alpha$は成長率

の場合、

\begin{align\*}
z_n & = \frac{y_n}{y_{n-1}} \\\\
& = \frac{T_n w_n}{T_{n-1} w_{n-1}} \\\\
(1 + \alpha) \frac{w_n}{w_{n-1}}
\end{align\*}
で、成長率$\alpha$を検出出来る


 * 周期$p$, 周期関数$s_n$とノイズの積
 * $y_n = s_n \dot w_n$, $s_n = s_{n-p}$

の場合、

\begin{align\*}
x_n & = \frac{y_n}{y_{n-p}} \\\\
& = \frac{s_n w_n}{s_{n-p} w_{n-p}} \\\\
& = \frac{w_n}{w_{n-p}}
\end{align\*}
となり周期分布を除去出来る

#### 移動平均
$2K+1$項の移動平均は
$ T_n = \frac{1}{2k+1} \sum_{j=-k}^k y_{n+k}$で定義

直線とノイズの和で表される場合

* $y_n = t_n + w_n$
* $t_n ~ a + bn$

$T_n$の平均は$t_n$と同じで分散は$w_n$の分散の$1/(2k+1)$となる。

 * 重み付き移動平均
    * $T_n = \sum_{j=-k}^{k} w_j y_{n-j}$



