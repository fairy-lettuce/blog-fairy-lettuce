---
title: "yukicoder No. 1310 - 量子アニーリング 解説"
date: 2020-12-07T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "yukicoder Advent Calendar Contest 2020 の 12/07 出題問題です。 みんながぐろふぉに出ている中の出題だったため[^1]、FA を狙えないかと思っていましたが残念ながら 3 番目の AC でした。 問題リンク https://yukico…"
aliases:
  - /entry/2020/12/07/233000
---

[yukicoder Advent Calendar Contest 2020](https://yukicoder.me/contests/300) の 12/07 出題問題です。  

みんながぐろふぉに出ている中の出題だったため[^1]、FA を狙えないかと思っていましたが残念ながら 3 番目の AC でした。

## 問題リンク

https://yukicoder.me/problems/no/1310

## 問題概要

$N$ 要素の $+1,\ -1$ のどちらかからなる数列 $s$ に対して、

<div>$\displaystyle E=-\sum_{i=1}^{N}s_i s_{i+1}$</div>

とします。ただし $s\_{N+1}=s\_{1}$ です。$2^{|E|}$ の総和を $998244353$ で割った余りを求めてください。

## 制約

$3\le N\le 2\times 10^{5}$

<!--more-->

## 考察・解法

サンプル 1 の解説を見ると、$E$ の値は $-3,\ 1$ がそれぞれ $2,\ 6$ 個ずつであることが分かります。$E=-3$ となるのは全ての $s\_{i},\ s\_{i+1}$ の符号が同じときで、$E=1$ となるのは $2$ 箇所のみ $s\_{i},\ s_{i+1}$ の符号が異なり、他は同じときです。  

さらに、$N=6$ で実験してみます。$s\_{i},\ s_{i+1}$ の符号が異なる個数が $i,\ (i=0,\ 2,\ 4,\ 6)$ 個である場合の数は、それぞれ $2\binom{6}{i}$ 通りです。これは $6$ 箇所の数の間から $i$ 箇所を選ぶ場合の数 (符号が逆のものを考慮しているため $2$ 倍) なので、一般の $N$ に関しては $2\binom{N}{i}$ 通りあることが分かります。なお、符号が異なる場所が奇数個であるような割り当て方は存在しません。  

さて、符号が異なる箇所が $i$ 個であるとき、$E=(-1)\times ((N-i)-i)=2i-N$ です。これより、答えは $0\le i\le N$ を満たす偶数 $i$ に関して  

<div>$\displaystyle 2\times 2^{|2i-N|}\binom{N}{i}$</div>

の総和と分かります。  

## 実装

二項係数は漸化式的に計算することで計算量を抑える一般的なテク[^2]を使えばよいです。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var n = cin.ReadInt();
			ModInt ans = 0;
			ModInt binom = 1;
			for (int i = 0; i <= n / 2; i++)
			{
				ans += 2 * ModInt.Pow(2, Abs(n - 4 * i)) * binom;
				binom *= n - 2 * i;
				binom *= n - 2 * i - 1;
				binom /= 2 * i + 2;
				binom /= 2 * i + 1;
			}
			Console.WriteLine(ans);
		}
```

</details>

ACコード: https://yukicoder.me/submissions/589996  

## 感想

二項係数をこねこねする問題好きです。典型であり教育的な問題ではないでしょうか。  

FA 取れなかったのとても悔しい……。まあ、この問題を 10 分程度でかたをつけることが出来るようになったので、純粋に成長を感じています。  

今回は早解きの練習としてペナ覚悟で submit でき、結果 3 人目の AC が取れました。AtCoder や Codeforces では Rated が多い寒色コーダーであっても、コンテストの結果を気にせずに立ち回りを研究できるのは yukicoder の強みだと個人的に思っています。  


[^1]: ぐろふぉ、翌日までにやることがあったのでパスしちゃいました。FAKE です……。
[^2]: これめちゃくちゃ典型な割に名前が付いていない気がします。誰かいい感じの名前付けてくれないでしょうか。