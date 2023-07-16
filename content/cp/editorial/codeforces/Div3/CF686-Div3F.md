---
title: "Codeforces Round #686 Div.3-F - Array Partition 解説"
date: 2020-11-25T06:47:38+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "これ E よりも簡単じゃないですか？ 問題リンク https://codeforces.com/contest/1454/problem/F 問題概要 要素からなる数列 があります。 を 以上の要素からなる つの部分列に分けます。 最初の部分列の最大値、真ん中の部分列の最小値、最…"
aliases:
  - /entry/2020/11/25/064738
---

<!-- 解説ブログ テンプレ -->

これ E よりも簡単じゃないですか？

## 問題リンク
https://codeforces.com/contest/1454/problem/F

## 問題概要
$n$ 要素からなる数列 $a$ があります。$a$ を $1$ 以上の要素からなる $3$ つの部分列に分けます。  
最初の部分列の最大値、真ん中の部分列の最小値、最後の部分列の最大値が全て等しくなるような分け方は存在しますか。存在するときはその分け方も出力してください。

## 制約
$1\le t\le 2\times 10^{4}$ (テストケース数)  
$3\le n\le 2\times 10^{5}$  
$1\le a_{i} \le 10^{9}$  
$\sum{n}\le 2\times 10^{5}$  

<!--more-->

## 考察
両端から攻めたら色々こんがらがりそうなので、真ん中に注目します。例として、サンプル 4 にある数列 $(2,\ 1,\ 4,\ 2,\ 4,\ 3,\ 3,\ 1,\ 2)$ をみてみます。  
天下り的ですが、$a_i$ の数値が大きい方から見てみます。まずは $4$ です。$4$ は $2$ つしかないため、$3$ つに分けたとき全ての部分列には入ることはありません。  
$3$ も $2$ 個しかないので条件を満たしません。  
$2$ は $3$ 個あります。このとき、$2$ 以上の数 $3,\ 4$ は<b>左右の部分列に含めることはできません</b>が、真ん中の部分列に含めることはできます。よって、$3,\ 4$ は真ん中の部分列に含めなければなりません。  
この条件を満たしつつ、$2$ が各部分列に含まれるように分けられないか確認します。$3,\ 4$ を含む区間であり $2$ よりも小さい値を<b>含まない</b>区間は $4,\ 2,\ 4,\ 3,\ 3$ のみですが、この区間を真ん中の部分列とすると、左の部分列は $2,\ 1$、右の部分列は $1,\ 2$ となります。これは条件を満たす分け方なので、答えになります。

## 解法
上の考察で見たように、数値が大きい順に数列を見ていって、真ん中の部分列に含めなければならない区間を大きくしながら条件を満たす区間が存在するか調べていきます。  

まず、一番大きい数値が $3$ つ存在したら、それが答えです。そうでなければ、一番大きい数値の存在する場所を仮の真ん中の区間にします。  
さらに大きい順に数値を見ていきます。今見ている数値を $p$ とおきます。$p$ が $3$ 個以上あるとき、真ん中の区間は仮の真ん中の区間(今見ている数値より大きいものが存在する最小の区間)を、$p$ が<b>隣にあれば伸ばし</b>ます[^1]。但し、伸ばした先が <b>$p$ が現れる左端・右端の場所</b>だった場合のみ戻します[^2]。この分け方で各区間の最小値と最大値を求め(セグメント木などで可能です)、全て同じ場合は条件を満たします。そうでないときは仮の真ん中の区間を伸ばして次に進みます。  

## 実装
セグ木を使えば区間 min/max は簡単に求まるので、本質はどのように分けると最適かを考える部分だと思います。  
なお、この解法では真ん中の区間は単調に広がっていくので、区間の最小値を更新するのは $O(N)$ で可能ですし、区間の最大値は両端からしか見ないので $O(N)$ 空間・時間で可能です。そのためセグメント木で殴るをしなければ $O(N)$ での計算も可能です(今回はしていません)。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public static void Solve(Scanner cin)
		{
			int _t = cin.ReadInt();
			for (int _i = 0; _i < _t; _i++)
			{
				var n = cin.ReadInt();
				var a = cin.ReadIntArray(n);
				var ans = Solve1(n, a);
				//if (!Check(n, a, ans))
				//{
				//	Console.WriteLine("Assertion Failed!");
				//}
			}
 
			//for (int i = 0; i < 100; i++)
			//{
			//	var rand = new Random((int)(DateTime.Now.Ticks << 32 >> 32));
			//	var p = new int[100];
			//	for (int j = 0; j < 100; j++)
			//	{
			//		p[j] = rand.Next(1, 100);
			//	}
			//	var ans = Solve1(100, p);
			//	if (!Check(100, p, ans))
			//	{
			//		Console.WriteLine("Assertion failed!");
			//	}
			//}
		}
 
		public static (int x, int y, int z) Solve1(int n, int[] a)
		{
			var minseg = new SegmentTree<int>(a, (x, y) => Math.Min(x, y), int.MaxValue);
			var maxseg = new SegmentTree<int>(a, (x, y) => Math.Max(x, y), 0);
			var seenLeft = n;
			var seenRight = -1;
			var place = new SortedDictionary<int, List<int>>();
			for (int i = 0; i < n; i++)
			{
				if (!place.ContainsKey(a[i])) place.Add(a[i], new List<int>());
				place[a[i]].Add(i);
			}
			var list = place.Select(p => (p.Key, p.Value)).ToList();
			list.Reverse();
 
			bool ok = false;
			(int x, int y, int z) ans = (-1, -1, -1);
			for (int i = 0; i < list.Count; i++)
			{
				if (seenLeft == n && seenRight == -1 && list[i].Value.Count >= 3)
				{
					Console.WriteLine("YES");
					Console.WriteLine($"{list[i].Value[1]} {1} {n - list[i].Value[1] - 1}");
					ans = (list[i].Value[1], 1, n - list[i].Value[1] - 1);
					ok = true;
				}
				else
				{
					if (list[i].Value.Count >= 3)
					{
						var t = list[i].Key;
						while (seenLeft > 0 && a[seenLeft - 1] == t)
						{
							seenLeft--;
						}
						if (seenLeft == list[i].Value[0]) seenLeft++;
						while (seenRight < n && a[seenRight] == t)
						{
							seenRight++;
						}
						if (seenRight - 1 == list[i].Value[list[i].Value.Count - 1]) seenRight--;
 
						var min = minseg.GetResult(seenLeft, seenRight);
						var maxl = maxseg.GetResult(0, seenLeft);
						var maxr = maxseg.GetResult(seenRight, n);
 
						if (min == t && maxl == t && maxr == t)
						{
							Console.WriteLine("YES");
							Console.WriteLine($"{seenLeft} {seenRight - seenLeft} {n - seenRight}");
							ans = (seenLeft, seenRight - seenLeft, n - seenRight);
							ok = true;
						}
					}
				}
 
				seenLeft = Math.Min(seenLeft, list[i].Value[0]);
				seenRight = Math.Max(seenRight, list[i].Value[list[i].Value.Count - 1] + 1);
 
				if (ok) break;
			}
			if (!ok) Console.WriteLine("NO");
			return ans;
		}
 
		public static bool Check(int n, int[] a, (int x, int y, int z) ans)
		{
			var minseg = new SegmentTree<int>(a, (x, y) => Math.Min(x, y), int.MaxValue);
			var maxseg = new SegmentTree<int>(a, (x, y) => Math.Max(x, y), 0);
			if (ans == (-1, -1, -1))
			{
				for (int x = 1; x < n - 1; x++)
				{
					for (int y = 1; x + y < n; y++)
					{
						var l = maxseg.GetResult(0, x);
						var m = minseg.GetResult(x, x + y);
						var r = maxseg.GetResult(x + y, n);
						if (l == m && m == r) return false;
					}
				}
				return true;
			}
			else
			{
				var l = maxseg.GetResult(0, ans.x);
				var m = minseg.GetResult(ans.x, ans.x + ans.y);
				var r = maxseg.GetResult(ans.x + ans.y, n);
				if (l == m && m == r) return true;
				else return false;
			}
		}
```

</details>

ACコード: https://codeforces.com/contest/1454/submission/99483106  

## 実装こばなし
ちなみに、この問題では注釈 1 と 2 のケースのどちらもバグらせており、なぜ落ちるか分からなかったためランダムチェッカーを書きました(上記の実装例にも残っています)[^3]。  
ランダムチェッカーを書くことは大変ですが、ケース生成が簡単で愚直解もすぐ書ける場合はある程度素早くできますし、何より落ちる理由を考える時間がすごく少なくなるため、結果的に使う時間が大幅に減ることも多いです。ランダムチェッカーのコーディングには慣れたほうがいいと強く思いました。

## 感想
ランダムチェッカーは雑に書いたけれどいいですね。今回はランダムチェッカーに非常に助けられた AC でした。


[^1]: これをしないと、$(1,\ 2,\ 7,\ 7,\ 8,\ 7,\ 7,\ 4,\ 4,\ 3)$ のようなケースで撃墜されます。$7$ を見る際に真ん中の区間を $8$ のみとしてしまうからです。
[^2]: これをしないと、$(4,\ 2,\ 4,\ 2,\ 4,\ 1,\ 4,\ 8,\ 4,\ 3)$ のようなケースで撃墜されます。$4$ を見る際に真ん中の区間を $4,\ 8,\ 4$ まで伸ばしてしまうからです。
[^3]: 上のふたつの撃墜ケースもランダムチェッカーで生成したものです。