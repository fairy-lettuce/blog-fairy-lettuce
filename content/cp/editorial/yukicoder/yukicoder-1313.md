---
title: "yukicoder No. 1313 - N言っちゃダメゲーム (4)"
date: 2020-12-10T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "問題リンク https://yukicoder.me/problems/no/1313 問題概要 先攻のプレイヤーであるあなたは から始めます。交互に のうちのどれかの整数を加算していきます。 以上の数字、または予め指定された危険な数字を言ってしまうと負けです。 あなたが勝つ場合…"
aliases:
  - /entry/2020/12/10/233000
---

<!-- 解説ブログ テンプレ -->

## 問題リンク

https://yukicoder.me/problems/no/1313

## 問題概要

先攻のプレイヤーであるあなたは $0$ から始めます。交互に $\[1,\ K\]$ のうちのどれかの整数を加算していきます。$N$ 以上の数字、または予め指定された<b>危険な数字</b>を言ってしまうと負けです。

あなたが勝つ場合は最初に宣言して勝てる数字を全て出力してください。あなたが負ける場合は $0$ を出力してください。

## 制約

- $N,\ K$ は整数
- $1\le K\lt N\le 2\times 10^{5}$
- $|S|=N-1$
- $S_{i}$ が `x` のときは $i$ は「危険な数字」で、`o` のときはそうではない

<!--more-->

## 考察・解法

典型的な Nim っぽいゲームなので、<b>Grundy 数</b>を考えます。以下、$g(i)$ を数 $i$ が<b>相手から渡された</b>ときに関する Grundy 数と定義します。  

今回は $N$ 以上の数を言っては負けるので、$N$ 未満で最も大きい危険な数字でない数を $x$ とすると $g(x)=0$ です。また、初期条件以外の Grundy 数は「その状態から遷移できる先のGrundy 数の $\mathrm{mex}$」で定義されます。  
まず危険な数字が存在しないものとして考えると、通常の場合は $g(N-1)=0,\ g(N-2)=1,\ \dots,\ g(N-K-1)=K$ と続き、$g(N-K-2)=0$ に戻る、というように $0,\ 1,\ \dots,\ K$ を順番に繰り返します。では、危険な数字を考慮して Grundy 数を考えてみます。

危険な数字はそもそも言った時点で負けであるので、最初から遷移できないものと考えてよいです。なので、Grundy 数は無限 (INF) 扱いしてもよいです。実装上は危険な数字が存在しない場合の Grundy 数の最大値 $K$ よりも大きい値 ($K+1$ など) を与えると良いでしょう。

この通りに Grundy 数を求めていき、$g(0)=0$ なら先攻であるあなたの負け、そうでないなら $g(i)=0$ を満たす $i=1,\ 2,\ \dots,\ K$ を全て出力すればよいです。

## 実装

$\mathrm{mex}$ は `multiset` やセグ木などで実装すると良いです。今回はセグ木で実装しました。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, k, s) = cin.ReadValue<int, int, string>();
			var grundy = new int[n];
			s = s.Reverse().Join();
			s += "o";

			var seg = new SegmentTree<(int count, int index)>(
				Enumerable.Zip(Enumerable.Repeat(0, k + 2), Enumerable.Range(0, k + 2), (x, y) => (count: x, index: y)),
				(x, y) => (x.count == 0) ? x : ((y.count == 0) ? y : (int.MaxValue, -1)),
				(int.MaxValue, -1));

			int max = -1;
			for (int i = 0; i < k; i++)
			{
				if (s[i] == 'o')
				{
					max++;
					grundy[i] = max;
					seg.Update(max, (seg[max].count + 1, max));
				}
				else
				{
					grundy[i] = k + 1;
					seg.Update(k + 1, (seg[k + 1].count + 1, k + 1));
				}
			}
			for (int i = k; i < n; i++)
			{
				if (s[i] == 'o')
				{
					grundy[i] = seg.GetResult(0, k + 2).index;
					seg.Update(grundy[i], (seg[grundy[i]].count + 1, grundy[i]));
				}
				else
				{
					grundy[i] = k + 1;
					seg.Update(grundy[i], (seg[grundy[i]].count + 1, grundy[i]));
				}

				var g = grundy[i - k];
				seg.Update(g, (seg[g].count - 1, g));
			}

			var ans = new List<int>();

			for (int i = k; i >= 1; i--)
			{
				if (grundy[n - i - 1] == 0) ans.Add(i);
			}

			ans.Sort();

			if (grundy[n - 1] == 0) Console.WriteLine(0);
			else
			{
				foreach (var e in ans)
				{
					Console.WriteLine(e);
				}
			}
		}
```

</details>

ACコード: https://yukicoder.me/submissions/591636  

## 感想

N言っちゃダメゲームに新作が出るとは思いもよりませんでした。びっくり。

変則 Nim の典型的な練習になると思います。面白かったです。

【追記】Grundy 数は実は必要なく、「<b>勝ち状態か負け状態か</b>」を管理するだけで解けます。遷移先に負け状態があれば勝ち状態、そうでなければ負け状態というようにする DP です。  
勝ち負けを管理するだけのほうが遥かに実装が楽ですね。不必要に難しい考察をする必要もないので、こちらのほうがエレガントな解法と言えます。