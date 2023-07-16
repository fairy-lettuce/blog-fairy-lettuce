---
title: "AtCoder Beginner Contest 198 E - Unique Color 解説"
date: 2021-04-12T02:44:15+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-04"]
description: "最近似た問題を見ていたのでその解説を見て難しい解法にハマりました。 Editorial と同じ解法と、自分の解法を両方紹介します。 問題リンク https://atcoder.jp/contests/abc198/tasks/abc198_e 問題概要 頂点の木が与えられます。頂…"
aliases:
  - /entry/2021/04/12/024415
---

<!-- 解説ブログ テンプレ -->

最近似た問題を見ていたのでその解説を見て難しい解法にハマりました。

Editorial と同じ解法と、自分の解法を両方紹介します。

## 問題リンク

https://atcoder.jp/contests/abc198/tasks/abc198_e

## 問題概要

$N$ 頂点の木が与えられます。頂点 $i$ は色 $C_i$ で塗られています。

頂点 $1$ から $x$ への最短パスに色 $C_x$ が頂点 $x$ 以外に現れないような頂点を<b>良い頂点</b>と呼びます。良い頂点を列挙してください。

## 制約

- $2\le N\le 10^5$  
- $1\le C\_i\le 10^5$  
- 与えられるグラフは木

<!--more-->

## 考察

純粋に考えると、$dp_{i,j}$ を頂点 $1,i$ のパス上に色 $j$ があるかどうかと定義して、木の根から下っていく木上の DP で解けますが、これでは実行時間制限に間に合いません。どうにかして間に合わせることを考えます。

## 解法 (1) - Editorial 解 (あたまいい)

$dp\_{i,j}$ は考察の段階では <code>bool</code> 値であり、情報に無駄があります。そこで、$dp_{i,j}$ をパス上に存在する色 $j$ の頂点数と定義しなおします。

こうすることで、DFS で親から子に下るときは色 $C\_i$ の個数を増やし、子から親へ登るときは色 $C_i$ の個数を減らすようにすれば $dp$ の添字 $i$ をなくして in-place に更新できるようになります。

計算量は $O(N+\max(C))$ になります。

## 実装 (1)

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var n = sr.ReadInt();
			var c = sr.ReadIntArray(n);
			var (a, b) = sr.ReadValueArray<int, int>(n - 1);
			var g = Enumerable.Repeat(0, n).Select(p => new List<int>()).ToArray();
			for (int i = 0; i < n - 1; i++)
			{
				g[--a[i]].Add(--b[i]);
				g[b[i]].Add(a[i]);
			}
			var dp = new int[100005];
			var ans = new bool[n];
			Dfs(0, -1, g, c, dp, ans);
			for (int i = 0; i < n; i++)
			{
				if (ans[i]) Console.WriteLine(i + 1);
			}
		}
 
		public void Dfs(int cur, int par, List<int>[] g, int[] c, int[] dp, bool[] ans)
		{
			dp[c[cur]]++;
			if (dp[c[cur]] == 1) ans[cur] = true;
			foreach (var e in g[cur])
			{
				if (e == par) continue;
				Dfs(e, cur, g, c, dp, ans);
			}
			dp[c[cur]]--;
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc198/submissions/21700986  

## 解法 (2) - 自分の解 (めんどい重実装)

頂点 $1,i$ のパス上の色を持つと空間計算量が $O(N\max(C))$ となってしまいます。ここでマージテクの使用を検討します。

木の根から降りていくのではマージテクは上手くできそうにないので、葉から登っていくことを考えます。$dp_i$ を、頂点 $i$ を根とした部分木における良い頂点の集合を、色別に <code>unordered_map</code> や <code>map</code> などで管理したものと定義します。ただし、色ごとに良い頂点は $1$ つとは限らないので、例えば C++ では <code>map<int, vector&lt;int&gt;></code> 
のような型を使います。

あとは、以下の要領でマージテクを行います。

- 子から親に登るときに子の集合をマージする。

    - 子の集合・親の集合のうち、色数が少ない方を大きい方へ移す。  
    - 各色について、その色にの良い頂点数が少ない方を大きい方へ移す。

- 親の頂点の色を親の集合に追加する。

    - もし子の集合からマージした集合にその色があるなら全部消して上書きする。

計算量は $O(N\log{N})$ だと思います[^1]。

## 実装 (2)

C# では <code>foreach</code> (range-based for) 中にループを回している要素そのものを変更できないので、実装に困りました。わざわざ <code>Dictionary<int, List&lt;int&gt;>.Keys</code> を実体化させています。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var n = sr.ReadInt();
			var c = sr.ReadIntArray(n);
			var (a, b) = sr.ReadValueArray<int, int>(n - 1);
			var dp = new Dictionary<int, List<int>>[n];
			var g = Enumerable.Repeat(0, n).Select(p => new List<int>()).ToArray();
			for (int i = 0; i < n - 1; i++)
			{
				g[--a[i]].Add(--b[i]);
				g[b[i]].Add(a[i]);
			}
			Dfs(0, -1, dp, c, g);
			var ans = new List<int>();
			foreach (var e in dp[0].Values)
			{
				foreach (var f in e)
				{
					ans.Add(f);
				}
			}
			ans.Sort();
			foreach (var e in ans)
			{
				Console.WriteLine(e + 1);
			}
		}
 
		public void Dfs(int cur, int par, Dictionary<int, List<int>>[] dp, int[] color, List<int>[] g)
		{
			dp[cur] = new Dictionary<int, List<int>>();
			foreach (var e in g[cur])
			{
				if (e == par) continue;
				Dfs(e, cur, dp, color, g);
				Merge(dp, cur, e);
			}
			dp[cur][color[cur]] = new List<int> { cur };
		}
 
		public void Merge(Dictionary<int, List<int>>[] dp, int par, int child)
		{
			if (dp[par].Count > dp[child].Count)
			{
				var k = dp[child].Keys.ToArray();
				foreach (var key in k)
				{
					var value = dp[child][key];
					if (dp[par].ContainsKey(key))
					{
						if (dp[par][key].Count > dp[child][key].Count)
						{
							foreach (var e in dp[child][key])
							{
								dp[par][key].Add(e);
							}
						}
						else
						{
							foreach (var e in dp[par][key])
							{
								dp[child][key].Add(e);
							}
							dp[par][key] = dp[child][key];
						}
					}
					else dp[par].Add(key, value);
				}
			}
			else
			{
				var k = dp[par].Keys.ToArray();
				foreach (var key in k)
				{
					var value = dp[par][key];
					if (dp[child].ContainsKey(key))
					{
						if (dp[par][key].Count > dp[child][key].Count)
						{
							foreach (var e in dp[child][key])
							{
								dp[par][key].Add(e);
							}
							dp[child][key] = dp[par][key];
						}
						else
						{
							foreach (var e in dp[par][key])
							{
								dp[child][key].Add(e);
							}
						}
					}
					else dp[child].Add(key, value);
				}
				dp[par] = dp[child];
			}
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc198/submissions/21671562  

## 感想

とある難しい問題の本質部分 (部分問題) がこの問題に帰着されるので、全く同じ問題を過去に考察していたことになります。その際は非常に難しく考えすぎていて分かりませんでしたし別の考え方で解いて未 AC でしたが、今回の出題でネタバレを食らってもいいやと思い解説を見てマージテク解を知りました。

そのマージテク解を実装 (100 行程度でちょっと重いかな……) したのですが、コンテスト後に単なる DFS で解けることを知ってけっこう唖然としました。私がここまで悩んでたのが 20 行の DFS で終わりだなんて……。

in-place な更新をする DP を完全に忘れていたのは大反省案件でした。

[^1]: もしかして $\log$ ふたつ付きます？マージテクの計算量解析分かりません……。