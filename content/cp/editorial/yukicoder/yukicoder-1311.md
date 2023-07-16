---
title: "yukicoder No.1311 - Reverse Permutation Index 解説"
date: 2020-12-08T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "yukicoder Advent Calendar Contest 2020 の 12/08 出題問題です。 一見すると の間に簡単な法則が成り立ちそうですが、どうなんでしょう。面白いですね。 問題リンク https://yukicoder.me/problems/no/1311…"
aliases:
  - /entry/2020/12/08/233000
---

<!-- 解説ブログ テンプレ -->

[yukicoder Advent Calendar Contest 2020](https://yukicoder.me/contests/300) の 12/08 出題問題です。  

一見すると $N,\ S$ の間に簡単な法則が成り立ちそうですが、どうなんでしょう。面白いですね。

## 問題リンク

https://yukicoder.me/problems/no/1311

## 問題概要

ある順列が辞書順で出てくる順番をインデックスと呼びます。また、$S$ 要素で $1$-indexed な順列 $a$ に対して $b$ が $a\_{b_{i}}=i (i=1,\ 2,\ \dots,\ s)$ を満たすとき、$b$ を $a$ の逆置換と呼びます。  
$S$ 要素の順列の中で、$N$ 番目の順列の逆置換のインデックスを求めてください。

## 制約

$0\le N\lt S! \lt 2^{64}$  
$1\le S\le 20$

<!--more-->

## 考察

インデックスを求める作業やインデックスから順列を復元する作業は $O(S)$ で可能です。  
愚直にそのプログラムを書きます。

## 解法

以下は全て $1$-indexed です。

まず、順列のインデックスを求める部分です。  
$S$ 要素を左から見ていきます。$i$ 番目の要素を決定するのに際して、右側 $S-i$ 要素の順列は全部で $(S-i)!$ 個存在します。これより、まだ使っていない数のうち $\lfloor N/(S-i)!\rfloor+1$ 番目のものが $i$ 番目の要素です。

次に、逆置換の順列を求めます。問題概要にある通りに実装します。

最後に、インデックスを求める部分です。  
左から $i$ 番目の要素がまだ使っていない数のうち $p$ 番目であるとき、インデックスへの寄与は $(p-1)\times (S-i)!$ なので、答えはこの総和です。

## 実装

まだ使っていない数のうち何番目、ということに関しては `set` (を C# で自作したもの) を使用しました。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, s) = cin.ReadValue<long, int>();
			factorial = new long[s + 1];
			factorial[0] = 1;
			for (int i = 0; i < s; i++)
			{
				factorial[i + 1] = factorial[i] * (i + 1);
			}

			var before = GetArray(n, s);
			var after = new int[s];
			for (int i = 0; i < s; i++)
			{
				after[before[i] - 1] = i + 1;
			}

			Console.WriteLine(GetNum(s, after));
		}

		public long GetNum(int s, int[] arr)
		{
			var unseen = new Set<int>();
			for (int i = 0; i < s; i++)
			{
				unseen.Add(i + 1);
			}

			long ret = 0;
			for (int i = 0; i < s; i++)
			{
				ret += factorial[s - i - 1] * unseen.LowerBound(arr[i]);
				unseen.Remove(arr[i]);
			}

			return ret;
		}

		public int[] GetArray(long n, int s)
		{
			var unseen = new Set<int>();
			for (int i = 0; i < s; i++)
			{
				unseen.Add(i + 1);
			}
			var ret = new int[s];
			for (int i = 0; i < s; i++)
			{
				ret[i] = unseen[(int)(n / factorial[s - i - 1])];
				unseen.Remove(ret[i]);
				n %= factorial[s - i - 1];
			}
			return ret;
		}
```

</details>

ACコード: https://yukicoder.me/submissions/590314  

## 感想

私はどう考えても ★2.5 はあると思いますが、微妙なところです。★2 と言われても、まあギリギリ……という感じです。