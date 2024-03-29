---
title: "Educational Codeforces Round 99 D - Sequence and Swaps 解説"
date: 2020-12-02T03:23:08+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "大爆死。反省を込めて解説ブログを書きます。 問題リンク https://codeforces.com/contest/1455/problem/D 問題概要 非負整数からなる数列 および非負整数 が与えられます。 である を swap することを任意の回数できます。 を広義単調増…"
aliases:
  - /entry/2020/12/02/032308
---

大爆死。反省を込めて解説ブログを書きます。

## 問題リンク  

https://codeforces.com/contest/1455/problem/D  

## 問題概要

非負整数からなる数列 $\{a_i\}$ および非負整数 $x$ が与えられます。  
$a\_{i}>x$ である $a_{i},\ x$ を swap することを任意の回数できます。$a$ を広義単調増加にするためには最小で何回の操作を行えばいいでしょうか。

## 制約

$1\le t\le 500$ (テストケース数)  
$1\le n\le 500$  
$0\le x,\ a_i\le 500$

<!--more-->

## 考察・解法

以下、$1$-indexed とします。
サンプルケース 1 を見ると、$a=(2,\ 3,\ 5,\ 4),\ x=1$ のとき $3$ 回が答えということで、$3$ 回で広義単調増加にするためにどうすればいいか考えてみます。  

$a=(2,\ 3,\ 5,\ 4),\ x=1$  
$a=(1,\ 3,\ 5,\ 4),\ x=2$ ($i=1$ で swap)  
$a=(1,\ 2,\ 5,\ 4),\ x=3$ ($i=2$ で swap)  
$a=(1,\ 2,\ 3,\ 4),\ x=5$ ($i=3$ で swap)  

これで $3$ 回で広義単調増加にできます。これを見ると、数列内で唯一広義単調増加でない $i=3,\ 4$ をきちんと広義単調増加にするために前から swap し続けています。  
サンプルケース 5 の $a=(81,\ 324,\ 218,\ 413,\ 324),\ x=18$ についても見てみます。  

 $a=(81,\ 324,\ 218,\ 413,\ 324),\ x=18$  
 $a=(18,\ 324,\ 218,\ 413,\ 324),\ x=81$ ($i=1$ で swap)  
 $a=(18,\ 81,\ 218,\ 413,\ 324),\ x=324$ ($i=2$ で swap)  
 $a=(18,\ 81,\ 218,\ 324,\ 324),\ x=413$ ($i=2$ で swap)  

これで $3$ 回で広義単調増加にできます。この場合は、広義単調増加になっていない $i=2,\ 3$ と $4,\ 5$ の間を広義単調増加にするのをそれぞれ $2,\ 1$ 回 swap することで達成しています。  

以上のふたつの例を見ると、$i=k,\ k+1$ で大小が逆転している場所は、その場所より前に現れる $a\_i$ で $a_i>x$ を満たす $i$ から $k$ までを順番に swap することで $i\le k+1$ までを広義単調増加にすることができます。  
なお、この操作をする前は $a\_k\ge a\_{k+1}$ であるため、新しい $x$ について $a_{k+1}\le x$ が成り立ちます。そのためこの操作をした後に次の広義単調増加でない場所の操作をする際、$k+1$ までの数列に操作する必要はなく、冗長な操作はしないことが分かります[^1]。  

ここで注意なのですが、例えば $a=(2,\ 2,\ 2,\ 5,\ 4),\ x=1$ のような数列に操作するとき、$a=(1,\ 2,\ 2,\ 5,\ 4),\ x=2$ とした次は $a=(1,\ 2,\ 2,\ 2,\ 4),\ x=5$ とするのが最小の手数です。つまり、広義単調増加でない場所の前で操作しようとしたときに<b>隣り合う数が同じ場合は飛ばす</b>必要があります。  

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
	public static int SolveAns(int n, int x, int[] a)
		{
			var rev = new List<int>();
			for (int i = 0; i < n - 1; i++)
			{
				if (a[i] > a[i + 1]) rev.Add(i);
			}
			bool ok = true;
 
			int ans = 0;
			for (int i = 0; i < rev.Count; i++)
			{
				var p = rev[i];
				while (p - 1 >= 0 && a[p - 1] > x)
				{
					p--;
				}
				for (int j = p; j <= rev[i]; j++)
				{
					if (a[j] < x) ok = false;
					if (a[j] == x) continue;
					var t = a[j];
					a[j] = x;
					x = t;
					ans++;
				}
			}
 
			for (int i = 0; i < n - 1; i++)
			{
				if (a[i] > a[i + 1]) ok = false;
			}
 
			if (ok) return ans;
			else return -1;
		}
```

</details>

ACコード: https://codeforces.com/contest/1455/submission/100039109  

## 感想
隣り合う数が同じときは飛ばす、が分からず非常に迷走しました。注意力が足りなかったです。中難易度の早解きが得意だからと先走ってしまいがちですが、もっと注意力を意識しながら問題を解く癖を付けたいです。  

ちなみにこれが問題を解いたときの惨状です。  

{{< tweet id="1333450701817536512" user="fairly_lettuce" >}}

反省。

[^1]: これが最小の操作数であることは未証明ですが……。