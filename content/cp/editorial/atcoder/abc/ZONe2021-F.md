---
title: "ZONeエナジー プログラミングコンテスト “HELLO SPACE” F - 出会いと別れ / Encounter and Farewell 解説"
date: 2021-05-02T00:40:52+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-05"]
description: "問題リンク https://atcoder.jp/contests/zone2021/tasks/zone2021_f 問題概要 個の頂点 について、 個の辺 を張って全域木を構成したいです。ただし、 について、 が のいずれかと一致するような辺 は張れません。 全域木が構成でき…"
aliases:
  - /entry/2021/05/02/004052
---

<!-- 解説ブログ テンプレ -->

## 問題リンク

https://atcoder.jp/contests/zone2021/tasks/zone2021_f

## 問題概要

$N=2\^n$ 個の頂点 $0,1,2,\dots,N-1$ について、$N-1$ 個の辺 $(i,j)$ を張って全域木を構成したいです。ただし、$(i,j)$ について、$i\oplus j$ が $A\_1,A\_2,\dots,A_M$ のいずれかと一致するような辺 $(i,j)$ は張れません。

全域木が構成できるか判定し、出来る場合は構成の仕方を $1$ つ示してください。

## 制約

- $N=2^n$ として、$1\le n\le 18$  
- $0\le M\le N-1$  
- $0\lt A\_1\lt A\_2\lt\dots\lt A_M\lt N$  

<!--more-->

## 考察

問題文を言い換えると、$n=\log_2{N}$ 個の $A$ に含まれない数の集合であって、全ての $1\le i\le N-1$ について、その集合の数をいくつか選んでその XOR 結合が $i$ と等しくなるような選び方のある集合が求まればいいことが分かります。これは XOR についての基底を求めることに他なりません。

公式解説では XOR の基底を利用した掃き出し法で判定していますが、ここでは Union-Find を使用した解を解説してみます。

## 解法

Union-Find を使用して、ある $A$ に含まれない数 $i$ が基底となるかの判定、およびそれまでに現れた基底を用いて作れる数を管理することを考えます。すなわち、基底 $b\_1,b_2,\dots,b_k$ があるとき、基底を XOR することで互いに作れるような数が連結になるような Union-Find を考えます。

例えば、$N=8,\ b=\\{1,5\\}$ のとき、$0,1,4,5$ および $2,3,6,7$ はそれぞれ基底をいくつか XOR すれば互いに作れますが、違うグループの数は基底をいくつか XOR しても作れません。例えば、$1,2$ は $\\{1,5\\}$ をどのように XOR しても互いに作れません。

このような Union-Find において、以下のような主張が成り立ちます。

> ある $A$ に含まれない数 $i$ が基底になる必要十分条件は、$0, i$ が同じ連結成分に無いことである。

これは、XOR の単位元である $0$ が含まれる連結成分は今までに見た基底の XOR 結合で作れる数の集合となっていることから示せます。

これで $i$ が基底かどうかの判定が出来ました。$i$ が基底ならば、互いに連結でない連結成分どうしを結ぶような辺 $(a,a\oplus i)$ を結べばよいです (基底は高々 $n\le 18$ 個であることから、このような頂点 $a$ は余裕をもって全探索出来ます)。

最終的に $0$ が含まれる連結成分の大きさが $N$ に等しければ結んだ辺を出力し、そうでなければ $-1$ を出力すればよいです。

計算量は $O(nN)=O(N \log N)$ です。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = sr.ReadValue<int, int>();
			var a = sr.ReadIntArray(m);
			var ok = new bool[n];
			for (int i = 1; i < n; i++)
			{
				ok[i] = true;
			}
			for (int i = 0; i < m; i++)
			{
				ok[a[i]] = false;
			}
			var uf = new UnionFind(n);
			var ans = new List<(int a, int b)>();
			for (int i = 0; i < n; i++)
			{
				if (!ok[i]) continue;
				if (uf.IsSame(0, i)) continue;
				for (int j = 0; j < n; j++)
				{
					if (uf.IsSame(j, j ^ i)) continue;
					uf.Unite(j, j ^ i);
					ans.Add((j, j ^ i));
				}
			}
			if (uf.Size(0) != n)
			{
				Console.WriteLine(-1);
				return;
			}
			foreach (var (s, t) in ans)
			{
				Console.WriteLine($"{s} {t}");
			}
		}
```

</details>

ACコード: https://atcoder.jp/contests/zone2021/submissions/22237765  

## 感想

コンテスト中に書いた解は掃き出し法を行って可能かの判定をした後に、Union-Find で全域木を構築しました。あとからよく考えたら Union-Find でそもそも基底を求めていることが分かったので、掃き出し法をしなくてもいいことに気付きました。
