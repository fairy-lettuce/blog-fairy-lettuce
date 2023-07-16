---
title: "Contest 2050 and Codeforces Round #718 (Div. 1 + Div. 2) D - Exploror Space 解説"
date: 2021-04-26T17:51:57+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2021", "2021-04"]
description: "謎制約。 問題リンク https://codeforces.com/contest/1517/problem/D 問題概要 のグリッドグラフが与えられます。 近傍で繋がっている各辺には道の長さが与えられています。 全ての について、 回移動を繰り返して最初の位置 に戻ってくるとき…"
aliases:
  - /entry/2021/04/26/175157
---

<!-- 解説ブログ テンプレ -->

謎制約。

## 問題リンク

https://codeforces.com/contest/1517/problem/D

## 問題概要

$n\times m$ のグリッドグラフが与えられます。$4$ 近傍で繋がっている各辺には道の長さが与えられています。

全ての $(i,j)$ について、$k$ 回移動を繰り返して最初の位置 $(i,j)$ に戻ってくるとき、移動経路の距離の最小値を答えてください。戻ってこれなければ -1 としてください。

## 制約

- $2\le n,m\le 500$  
- $1\le k\le 20$  
- $1\le (各辺の長さ)\le 10^6$  

<!--more-->

## 考察・解法

まず、自明な考察として、$k$ が奇数のときはどのような場合でも元の頂点には戻ってこれません。グリッドグラフが二部グラフであるためです。以下、$k$ は偶数とします。

$k$ が謎に小さい制約なので、各頂点について愚直な DP とか Dijkstra をしたら TLE しそうです。$(i,j)$ から訪れる可能性がある頂点は (何回目に訪れるかの情報を含めて) 高々 $O(k\^3)$ 個ですが、$\tilde{O}(nmk^3)$  [^1]は (高速でない言語では) 間に合いそうにはありません。

そこで、いくつかの重要な観察に基づいて高速な DP を考えます。

とりあえず愚直な DP を考えてみましょう。各 $(i,j)$ について別々に探索します。$dp\_{i,x,y}$ を、$(i,j)$ から $(x,y)$ まで $i$ 回で移動したときの移動距離の最小値とします。頂点 $(i,j)$ に対する答えは $dp_{k,i,j}$ ですが、この DP ではどの頂点から始めたかによって $(i,j)$ の値が当然違うので、$nm$ 回のループが必要になってしまいます。

$nm$ 回のループを避けるために、全頂点のどこかから $(x,y)$ まで $i$ 回で移動したときの移動距離の最小値を考えてみます。このとき、全頂点のどこかから $(i,j)$ までを $k/2$ 回で移動したときの移動距離の倍が $(i,j)$ の答えになっています。

画像で言うと下のような感じです。


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210426174320">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/f/fairy_lettuce/20210426/20210426174320.jpg" alt="20210426174320">
</a>

$dp_{i,x,y}$ を「全頂点のどこかから $(x,y)$ まで $i$ 回で移動したときの移動距離の最小値」とすると、これはどの頂点から始めるかに依存しないので、計算量 $O(nmk)$ で計算でき、各 $(i,j)$ の答えはこの DP テーブルから $O(1)$ で計算可能です。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		int[] dx = { 1, 0, -1, 0 };
		int[] dy = { 0, 1, 0, -1 };
 
		public void Solve()
		{
			var (n, m, k) = sr.ReadValue<int, int, int>();
			var a = new long[n][];
			for (int i = 0; i < n; i++)
			{
				a[i] = sr.ReadLongArray(m - 1);
			}
			var b = new long[n - 1][];
			for (int i = 0; i < n - 1; i++)
			{
				b[i] = sr.ReadLongArray(m);
			}
			if (k % 2 == 1)
			{
				for (int i = 0; i < n; i++)
				{
					Console.WriteLine(Enumerable.Repeat(-1, m).Join(" "));
				}
				return;
			}
			var ans = Enumerable.Repeat(0, k / 2 + 1)
				.Select(p => Enumerable.Repeat(0, n)
					.Select(p => Enumerable.Repeat(long.MaxValue / 3, m).ToArray())
				.ToArray()).ToArray();
			for (int i = 0; i < n; i++)
			{
				for (int j = 0; j < m; j++)
				{
					ans[0][i][j] = 0;
				}
			}
			for (int i = 0; i < k / 2; i++)
			{
				for (int x = 0; x < n; x++)
				{
					for (int y = 0; y < m; y++)
					{
						if (x < n - 1)	ans[i + 1][x + 1][y].Chmin(ans[i][x][y] + b[x][y]);
						if (x > 0)		ans[i + 1][x - 1][y].Chmin(ans[i][x][y] + b[x - 1][y]);
						if (y < m - 1)	ans[i + 1][x][y + 1].Chmin(ans[i][x][y] + a[x][y]);
						if (y > 0)		ans[i + 1][x][y - 1].Chmin(ans[i][x][y] + a[x][y - 1]);
					}
				}
			}
			for (int i = 0; i < n; i++)
			{
				for (int j = 0; j < m; j++)
				{
					ans[k / 2][i][j] *= 2;
				}
			}
			for (int i = 0; i < n; i++)
			{
				Console.WriteLine(ans[k / 2][i].Join(" "));
			}
		}
```

</details>

ACコード: https://codeforces.com/contest/1517/submission/114048500  

## 感想

$k$ の値が小さいから見る頂点も少ないし、多点 Dijkstra 通るかな……でごねごねしていて沼にはまりました。まあ $\log$ が付くと $10\^9$ を軽く超えるので通るわけないですが、$O(nmk^3)$ は一部の C++ 使いが通していました。$n,m\le 100,\ k\le 100$ じゃない理由って何かあるんでしょうか。

反省です。

[^1]: $\tilde{O}$ は計算量の $\log$ を無視するという意味です。
