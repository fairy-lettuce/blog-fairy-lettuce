---
title: "Educational Codeforces Round 91 E - Merging Towers 解説"
date: 2021-05-20T19:35:25+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2021", "2021-05"]
description: "解説記事や Editorial などをざっくり見ても自分の解法の解説が無かったので。 問題リンク https://codeforces.com/contest/1380/problem/E 問題概要 個の皿が 個のタワーに置かれています。皿は直径がそれぞれ であり、直径が である…"
aliases:
  - /entry/2021/05/20/193525
---

<!-- 解説ブログ テンプレ -->

解説記事や Editorial などをざっくり見ても自分の解法の解説が無かったので。

## 問題リンク

https://codeforces.com/contest/1380/problem/E

## 問題概要

$n$ 個の皿が $m$ 個のタワーに置かれています。皿は直径がそれぞれ $1,2,\dots,n$ であり、直径が $i$ である皿はタワー $t_i$ に置かれています。また、各タワーには上から直径が小さい順に皿が置かれています。

あなたは $m$ 個のタワーのうち $1$ つに全ての皿が乗っているようにしたいです。タワーは以下の操作を何回か行うことでまとめます。

- 好きな $i,j\ (1\le i,j\le m,\ i\ne j)$ を選ぶ。タワー $i$ から上から何個か (全部でも良い) の皿を取り、同じ順番でタワー $j$ の上に置く。このとき、動かす皿は全て操作前のタワー $j$ の一番上の皿よりも小さくてはならない。

タワーを $1$ つにまとめるのに必要な操作回数の最小値を、タワーをまとめる<b>難易度</b>と呼ぶことにします。

さて、$m-1$ 個のクエリ $a\_i,b\_i$ が与えられます。$i$ 個目のクエリはタワー $b\_i$ の皿を全てタワー $a\_i$ の皿とまとめ、新たなタワー $a_i$ にすることを意味します。新たなタワーも皿は上から小さい順になるようにします。これは難易度には関係しません。

全ての $k=0,1,2,\dots,m-1$ について、$k$ 番目のクエリまでを処理した状態でのタワーをまとめる<b>難易度</b>を求めてください。

## 制約

- $2\le m\le n\le 2\times 10^5$
- $1\le t_i\le m$
- $1,2,\dots,m$ までの整数がそれぞれ少なくとも 1 回以上 $t_i$ に現れる。
- $1\le a\_i,b\_i\le m,\ a\_i\ne b_i$
- クエリ $a\_i,b_i$ はどちらのタワーもそのクエリの時点で存在するようなものが与えられる。

<!--more-->

## 考察

適当に実験してみます。サンプルを例にしてみます。

最初の状態では、$t=(1,2,3,3,1,4,3)$ で、各タワーの皿は $(5,1),(2),(7,4,3),(6)$ となっています。これは以下のような操作で $5$ 回で 1 つのタワーに皿を集められます。

- 皿 $1$ 以上をタワー $2$ に移す。$(5),(2,1),(7,4,3),(6)$ となる。
- 皿 $2$ 以上をタワー $3$ に移す。$(5),(),(7,4,3,2,1),(6)$ となる。
- 皿 $4$ 以上をタワー $1$ に移す。$(5,4,3,2,1),(),(7),(6)$ となる。
- 皿 $5$ 以上をタワー $4$ に移す。$(),(),(7),(6,5,4,3,2,1)$ となる。
- 皿 $6$ 以上をタワー $3$ に移す。$(),(),(7,6,5,4,3,2,1),()$ となる

これを見ると、必要な操作回数は「皿 $i$ 以上を他のタワーに移す」操作が必要となっている $i$ は、$t\_i\ne t_{i+1}$ となっている $i$ であると推測できます。実際に、このサンプルのクエリ処理後もそうやって計算できることは容易に確認できるでしょう。

では、$t\_i\ne t_{i+1}$ となる $i$ の個数を高速に数え上げる方法を考えていきます。

## 解法

<b>部分永続 Union Find</b> を使用します。詳しくは各自調べてください。ここでは、以下のようなクエリを扱えるデータ構造であるとします。

- $\mathrm{unite}(x,y)$  
頂点 $x,y$ を連結する。

- $\mathrm{find}(x,t)$  
<b>$t$ 回目の $\mathrm{unite}$ クエリが終わった時点で</b>、頂点 $x$ の連結成分における根を答える。

- $\mathrm{same}(x,y,t)$  
<b>$t$ 回目の $\mathrm{unite}$ クエリが終わった時点で</b>、頂点 $x,y$ が連結かどうかを答える。

それぞれのクエリは全て、頂点数を $N$ として $O(\log N)$ 時間で答えられるものとします。

では、クエリが $k$ 個終わった時点での $t\_i\ne t_{i+1}$ となる $i$ の個数を数える方法を考えます。

それぞれの $i=1,2,\dots,n-1$ について、$t\_i\ne t\_{i+1}$ であるのは $t\_i$ と $t\_{i+1}$ の属するタワー (つまり、Union Find における根としてよいです) がマージされたタイミングよりも前です。そのタイミング以降は常に $t\_i= t_{i+1}$ です。このタイミングは、部分永続 Union Find の $\mathrm{same}$ クエリによって二分探索が可能です。

よって、各 $i$ のマージされるタイミングが $O(\log^2 m)$ にて計算出来ます。あとは imos 法によって答えを計算すればよいです。

計算量は $O(m+n\log^2 m)$ です。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = sr.ReadValue<int, int>();
			var a = sr.ReadIntArray(n);
			var uf = new PersistentUnionFind(m);
			for (int i = 0; i < m - 1; i++)
			{
				var (u, v) = sr.ReadValue<int, int>();
				uf.Unite(--u, --v);
			}
			var when = new int[m];
			for (int i = 0; i < n - 1; i++)
			{
				var ok = m - 1;
				var ng = -1;
				while (ok - ng > 1)
				{
					var mid = ng + (ok - ng) / 2;
					if (uf.IsSame(a[i] - 1, a[i + 1] - 1, mid)) ok = mid;
					else ng = mid;
				}
				when[ok]++;
			}
			var sum = new int[m + 1];
			for (int i = 0; i < m; i++)
			{
				sum[i + 1] = sum[i] + when[i];
			}
			for (int i = 1; i <= m; i++)
			{
				Console.WriteLine(n - 1 - sum[i]);
			}
		}
```

</details>

ACコード: https://codeforces.com/contest/1380/submission/116487345  

## 感想

他の解説の解法について、以下にも書いておきます (念の為隠しています)。
<details><summary><u><b>展開する</b></u></summary>

- 公式の Editorial (クエリを木として扱って LCA をする)
https://codeforces.com/blog/entry/80054

- じゅぴろさん・kmjp さんの解説 (マージテク)

じゅぴろさんのほう


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://jupiro.hatenablog.com/entry/2020/07/13/023428"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


kmjp さんのほう


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://kmjp.hatenablog.jp/entry/2021/04/05/0930"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


</details>

他所で解説の無い解法があればなるべく解説記事として後に残したいと考えているのでこの記事を執筆した感じです。
