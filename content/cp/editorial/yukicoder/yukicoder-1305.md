---
title: "yukicoder No. 1305 - Speak of the Devil 解説"
date: 2020-12-02T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "yukicoder Advent Calendar Contest 2020 の 12/02 出題問題です。 今気付いたんですが、これ yukicoder の記念すべき第 300 回目のコンテストらしいですね。 問題リンク https://yukicoder.me/problem…"
aliases:
  - /entry/2020/12/02/233000
---

[yukicoder Advent Calendar Contest 2020](https://yukicoder.me/contests/300) の 12/02 出題問題です。  
今気付いたんですが、これ yukicoder の記念すべき第 300 回目のコンテストらしいですね。  

## 問題リンク

https://yukicoder.me/problems/no/1305

## 問題概要

太郎君を呼ぶためにあなたは以下のうちのどちらかの行動をします。

- 噂をする。$1$ 分後に太郎君は $1/p$ の確率で来る。そうでない確率 $(p-1)/p$ のときは再度どちらかの行動をする。
- 電話をして呼び出す。太郎君は $q$ 分後に必ず来る。

太郎君が来るまでの時間の期待値の最小値を求めてください。

## 制約

$1\le p,\ q\le 10^{9}$  
入力は整数  

<!--more-->

## 考察・解法

噂をしたあとに電話をするのはどう見ても冗長なので、最初からどちらかの行動をしてそのうち小さい方が答えであると察せます。電話をするときの期待値は $q$ なので、噂をし続けるときの期待値を求めたいです。  

噂をし続けるとき、$k$ 分後に来る確率は $\displaystyle {\left(\frac{p-1}{p}\right)}^{k-1}\frac{1}{p}$ なので、求める期待値は $\displaystyle \frac{1}{p}\sum_{n=1}^\infty n{\left(\frac{p-1}{p}\right)}^{n-1}$ です。  
ここで、$f\_{n}(x)=x^{n}$ とすると答えは $\displaystyle \frac{1}{p}\sum\_{n=1}^\infty f\_{n}'\left(\frac{p-1}{p}\right)$ です。ただし、$f\_{n}'(x)$ は $f_{n}(x)$ の導関数です。  
ところで、$|x|\lt 1$ のとき $\displaystyle \sum\_{n=1}^\infty f\_{n}(x)=\frac{x}{1-x}$ であるため、$\displaystyle \sum\_{n=1}\^\infty f\_{n}'(x)=\frac{d}{dx}\left(\frac{x}{1-x}\right)=\frac{1}{(1-x)^2}$ です。  

以上より、噂をし続けるの期待値は $\displaystyle \frac{1}{p}\sum\_{n=1}^\infty\ f\_{n}'\left(\frac{p-1}{p}\right)=\frac{1}{p}\frac{1}{\left(1-\left(\frac{p-1}{p}\right)\right)^2}=p$ と分かりました。  

よって、答えは $\min(p,\ q)$ です。  

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public static void Solve(Scanner cin)
		{
			var (p, q) = cin.ReadValue<long, long>();
			Console.WriteLine(Math.Min(p, q));
		}
```

</details>

ACコード: https://yukicoder.me/submissions/588045  

## 感想

次数倍が掛かった等比級数の総和は高校数学でも典型です。答えは簡単な式で表されますが、そこに至るまでの考察は普通に ★2.5 相当はあると私は思います。