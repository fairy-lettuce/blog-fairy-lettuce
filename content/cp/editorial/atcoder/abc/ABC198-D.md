---
title: "AtCoder Beginner Contest 198 D - Send More Money 解説"
date: 2021-04-12T01:20:58+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-04"]
description: "久々に ABC でやったらいけないミスした……。 問題リンク https://atcoder.jp/contests/abc198/tasks/abc198_d 問題概要 覆面算 を解いてください。 制約 は英小文字のみからなる。"
aliases:
  - /entry/2021/04/12/012058
---

<!-- 解説ブログ テンプレ -->

久々に ABC でやったらいけないミスした……。

## 問題リンク

https://atcoder.jp/contests/abc198/tasks/abc198_d

## 問題概要

覆面算 $S\_1+S\_2=S_3$ を解いてください。

## 制約

- $1\le |S_i|\le 10$  
- $S_i$ は英小文字のみからなる。  

<!--more-->

## 考察

単純に考えたら、<code>a-z</code> の各文字に数字 <code>0-9</code> を割り振るので $10^{26}$ 全探索すればよいかと思うかもしれませんが、それでは間に合いません。

しかし、ここで考慮すべき重要な条件として、問題文 (厳密な定義) の 3 つ目の条件があります。

> $S\_i$ の $x$ 文字目と $S\_j$ の $y$ 文字目が等しいとき、またその時に限り、$N\_i'$ の $x$ 文字目と $N_j'$ の $y$ 文字目が等しい。

この条件より、<code>0-9</code> を割り当てられるのはたかだか $10$ 文字であり、重複は認められません。

よって、$10$ 文字よりも多い文字が登場するときは割り当てることは不可能で、それ以外のときはたかだか $10!$ 回のシミュレーションで十分です。

## 解法

登場する英小文字が $11$ 個以上の場合は <code>UNSOLVABLE</code> を出力します。そうでない場合について、英小文字に $\[0,9\]$ までの数を割り当てておきます。

数を割り当てられたら、$a=(0,1,2,3,4,5,6,7,8,9)$ という配列を用意し、順列についての全探索を行います[^1]。これは例えば C++ なら <code>next_permutation</code> などで可能です。そして $i$ 番目の文字を $a_i$ とした場合について、

- 先頭に $0$ が無いか  

- 覆面算は成立するか  

をシミュレーションすればよいです。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var dig = new long[] { 1, 10, 100, 1000, 10000, 100000, 1000000, 10000000, 100000000, 1000000000 };
			var s = sr.ReadStringArray(3);
			var use = new Dictionary<char, int>();
			var rev = new List<char>();
			var prefix = new List<bool>();
			for (int i = 0; i < 3; i++)
			{
				for (int j = 0; j < s[i].Length; j++)
				{
					if (!use.ContainsKey(s[i][j]))
					{
						rev.Add(s[i][j]);
						use.Add(s[i][j], use.Count);
						prefix.Add(false);
					}
					if (j == 0) prefix[use[s[i][j]]] = true;
				}
			}
			if (use.Count > 10)
			{
				Console.WriteLine("UNSOLVABLE");
				return;
			}
			var a = Enumerable.Range(0, 10).ToArray();
			do
			{
				var l = new long[3];
				var ok = true;
				for (int i = 0; i < use.Count; i++)
				{
					if (prefix[i] && a[i] == 0) ok = false;
				}
				if (!ok) continue;
				for (int i = 0; i < 3; i++)
				{
					for (int j = 0; j < s[i].Length; j++)
					{
						l[i] += dig[s[i].Length - j - 1] * a[use[s[i][j]]];
					}
				}
				if (l[0] + l[1] == l[2])
				{
					for (int i = 0; i < 3; i++)
					{
						Console.WriteLine(l[i]);
					}
					return;
				}
			} while (NextPermutation(a));
			Console.WriteLine("UNSOLVABLE");
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc198/submissions/21698038  

## 反省文

ずーーーーーっと割り当てられる数字は重複しないという条件を見落としていて計算量が落ちないと勘違いしていました。コンテスト本番では ICPC にあった類題を検索して見つけてきて、その AC コードをいじってなんとか通しました。

そもそもその解説を見ても何をすればいいのか分かっていないままだったのでかなり沼でした。流石に類題の解説を見たら解法は分からないといけないです。反省。

## 感想

D 問題相当だけど難しいと思います。$10!$ を書く勇気、とか TL 5 sec とか言われていましたが、まあ $10!=3628800$ なので実行時間制限はあまり難しく考えずに書いてもいいと思います。どちらかというと正直順列全探索が見えたら終わりな問題ですね。私は見えませんでした。

[^1]: 当然ですが、文字が $10$ 個未満しか登場しないときでも $10$ 個の数字を用意しないといけません。
