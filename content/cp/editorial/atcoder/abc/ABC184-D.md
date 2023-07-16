---
title: "AtCoder Beginner Contest 184 D - increment of coins 解説"
date: 2020-11-23T00:42:26+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "この問題だけどうして問題名の最初が英小文字なのか気になってしまい夜も眠れません。 問題リンク https://atcoder.jp/contests/abc184/tasks/abc184_d 問題概要 袋の中に金貨が 枚、銀貨が 枚、銅貨が 枚入っています。 袋の中から硬貨をラ…"
aliases:
  - /entry/2020/11/23/004226
---

この問題だけどうして問題名の最初が英小文字なのか気になってしまい夜も眠れません。

## 問題リンク
https://atcoder.jp/contests/abc184/tasks/abc184_d

## 問題概要
袋の中に金貨が $A$ 枚、銀貨が $B$ 枚、銅貨が $C$ 枚入っています。  
袋の中から硬貨をランダムに選び、それと同じ種類の硬貨をもう $1$ 枚入れるのをいずれかの種類の硬貨が $100$ 枚になるまで繰り返します。  
操作回数の期待値を求めてください。

## 制約
$0\le A,\ B,\ C\le 99$  
$A+B+C\ge 1$

<!--more-->

## 考察
DP できそうなので、DP を考えます。袋の中にある枚数が $a,\ b,\ c$ 枚のときの期待値を $dp_{a,\ b,\ c}$ と表しましょう。  
まず、$a,\ b,\ c$ のいずれかが $100$ になるときはもう操作ができないので、$dp_\{a,\ b,\ c}=0$ です。次に、$a=b=c=99$ のときは、$dp_{a,\ b,\ c}=1$ です。  
サンプル 2 を参考にすると、$(a,\ b,\ c)=(98,\ 99,\ 99)$ のときは金貨を選んだときのみ $2$ 回、そうでないときは $1$ 回です。これは金貨を選んだときは $dp\_{99,\ 99,\ 99}+1$ 回、そうでないときは $dp_{98,\ 100,\ 99}+1$ 回と解釈できます。つまり、増えた硬貨が遷移元である DP であると考えることができます。そしてこれらの期待値をそれぞれの硬貨毎の確率を掛けて足し合わせると答えになります。  
これを一般化した変遷を考えれば答えです。

## 解法
$a,\ b,\ c$ のいずれかが $100$ になるとき、$dp_{a,\ b,\ c}=0$ です。これが初期条件です。  
それ以外の $a,\ b,\ c$ に関しては、以下の遷移により求めます。  

$\displaystyle dp\_{a,\ b,\ c}=\frac{a}{a+b+c} dp\_{a+1,\ b,\ c}+\frac{b}{a+b+c} dp\_{a,\ b+1,\ c}+\frac{c}{a+b+c} dp_{a,\ b,\ c+1}+1$

## 実装
DP は BFS/DFS でも、適当に三重ループを回すのでもいいです。私は DFS しました。
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public static void Solve(Scanner cin)
{
	var (a, b, c) = cin.ReadValue<int, int, int>();
	var dp = new double[101 * 101 * 101];
	var q = new Queue<int>();
	q.Enqueue(Change(99, 99, 99));
	while (q.Count > 0)
	{
		var cur = q.Dequeue();
		if (dp[cur] > 0) continue;
		var (curi, curj, curk) = Unchange(cur);
		var cursum = curi + curj + curk;
		dp[cur] = (curi * (dp[Change(curi + 1, curj, curk)] + 1)
			+ curj * (dp[Change(curi, curj + 1, curk)] + 1)
			+ curk * (dp[Change(curi, curj, curk + 1)] + 1)) / cursum;
		if (curi - 1 >= a) q.Enqueue(Change(curi - 1, curj, curk));
		if (curj - 1 >= b) q.Enqueue(Change(curi, curj - 1, curk));
		if (curk - 1 >= c) q.Enqueue(Change(curi, curj, curk - 1));
	}
	Console.WriteLine(dp[Change(a, b, c)]);
}

public static int Change(int i, int j, int k)
{
	return i * 101 * 101 + j * 101 + k;
}

public static (int i, int j, int k) Unchange(int x)
{
	return (x / (101 * 101), x / 101 % 101, x % 101);
}
```

</details>

ACコード: https://atcoder.jp/contests/abc184/submissions/18317758  

## 感想
D 一瞬で解けたからこの問題だけで順位つけてほしかったです(わがまま)。