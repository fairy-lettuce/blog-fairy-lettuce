---
title: "Educational Codeforces Round 102 E - Minimum Path 解説"
date: 2021-01-15T03:42:33+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2021", "2021-01"]
description: "Educational Codeforces 初めての Unrated 参加でした。見事に出来ないといけない問題が解けずに Educate されました。 問題リンク https://codeforces.com/contest/1473/problem/E 問題概要 頂点 辺の重…"
aliases:
  - /entry/2021/01/15/034233
---

<!-- 解説ブログ テンプレ -->

Educational Codeforces 初めての Unrated 参加でした。見事に出来ないといけない問題が解けずに Educate されました。

## 問題リンク

https://codeforces.com/contest/1473/problem/E

## 問題概要

$n$ 頂点 $m$ 辺の重み付き有向連結グラフが与えられます。ここで、二頂点間のパスの距離を、通る辺の重みの総和に、重みの最小値を足して、最大値を引いたものと定義します。頂点 $1$ から各頂点への距離の最小値を求めてください。

## 制約

- $2\le n\le 2\times 10^5$  
- $1\le m\le 2\times 10^5$  
- $1\le w_i\le 10^9$  

<!--more-->

## 考察

まず、問題文を次のように読み替えます。

- 二頂点間の最小距離を、通る辺の重みの総和に、通る辺の中から好きな辺の重みを選んで足し、好きな辺の重みを選んで引いたものの中の最小値とする。

足す辺の重みは最大値を選べば良いですし、引く辺の重みは最小値を選べば良いですので、このように考えても正当です。

さて、この「好きな辺」を選ぶパートが面倒ですが、これは 通常の (区間最小値や最大値を使わない) DP であればどのように考えるでしょうか。  
DP の典型的な考え方に「<b>状態を次元として増やす</b>」というものがあります。ですが、この問題は通常の DP ではなく最短路問題です。最短路問題において状態を増やすテクニックは<b>超頂点</b>に対応しますね。ということで、今回は超頂点を用います。  
「引く辺の重み」を選んだかどうか、「足す辺の重み」を選んだかどうかの状態を各頂点について管理しておきます。そして、「引く辺の重み」を選んだ時は距離を増やさず、逆に「足す辺の重み」を選んだ時は距離を倍増やすというように変遷すれば良いです。  

頂点数・辺の数は高々定数倍の増加なので、計算量は通常の Dijkstra 法と同じであり十分高速です。

## 解法

頂点を $4$ 倍に増加させます。各頂点について、距離にある辺の重みを足した場合は $+2$、距離からある辺の重みを引いた場合は $+1$ するとして、$0,\ 1,\ 2,\ 3$ の $4$ つの状態を保持しておきます。  

あとは、その辺を距離に足す場合 (状態 $0,\ 1$ からそれぞれ状態 $2,\ 3$ へ変遷する場合) は $2w_i$ を足し、その辺を距離から引く場合 (状態 $0,\ 2$ からそれぞれ状態 $1,\ 3$ へ変遷する場合) は $0$ を足すとして通常通り Dijkstra 法を適用すれば良いです。  

ただし、その辺が最大値でも最小値でもあるケース (最初の頂点の隣の頂点など) もあるため、状態 $0$ から $3$ へ遷移するときも考慮する必要があります。増加する距離の値は $w_i$ です。

## 実装

通常の Dijkstra 法の実装をいじり、優先度付きキューに状態を持たせる実装 (辺を陽に構築しない) と、超頂点を作って実際に辺を陽に構築する実装があります。どちらのほうが楽かは個人差があると思いますが、私は Dijkstra 法のライブラリを貼るだけにするように辺を陽に構築しました。ただし、辺を張る際に多少面倒になってしまいます。  
ライブラリを貼らず、いちから書くとしたら陽に辺を構築しないほうが楽かなと個人的には感じます。

<details><summary><u><b>実装を展開する (辺を陽に構築した解)</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = sr.ReadValue<int, int>();
			var (u, v, w) = sr.ReadValueArray<int, int, long>(m);
			var graph = new Graph.DirectedWeightedGraph(n * 4);
			for (int i = 0; i < m; i++)
			{
				u[i]--;
				v[i]--;
				for (int j = 0; j < 4; j++)
				{
					graph.AddEdge(u[i] + j * n, v[i] + j * n, w[i]);
					graph.AddEdge(v[i] + j * n, u[i] + j * n, w[i]);
				}

				graph.AddEdge(u[i], v[i] + n, 2 * w[i]);
				graph.AddEdge(v[i], u[i] + n, 2 * w[i]);
				graph.AddEdge(u[i] + 2 * n, v[i] + 3 * n, 2 * w[i]);
				graph.AddEdge(v[i] + 2 * n, u[i] + 3 * n, 2 * w[i]);

				graph.AddEdge(u[i], v[i] + 2 * n, 0);
				graph.AddEdge(v[i], u[i] + 2 * n, 0);
				graph.AddEdge(u[i] + n, v[i] + 3 * n, 0);
				graph.AddEdge(v[i] + n, u[i] + 3 * n, 0);

				graph.AddEdge(u[i], v[i] + 3 * n, w[i]);
				graph.AddEdge(v[i], u[i] + 3 * n, w[i]);
			}
			var dijkstra = new Graph.Dijkstra(graph);
			var ans = dijkstra.GetDistanceFrom(0);
			Console.WriteLine(ans.Skip(3 * n + 1).Join(" "));
		}
```

</details>

ACコード: https://codeforces.com/contest/1473/submission/104355355  
<details><summary><u><b>実装を展開する (辺を陽に構築していない解)</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = sr.ReadValue<int, int>();
			var (u, v, w) = sr.ReadValueArray<int, int, long>(m);
			var g = Enumerable.Repeat(0, n).Select(p => new List<(int to, long weight)>()).ToArray();
			for (int i = 0; i < m; i++)
			{
				g[--u[i]].Add((--v[i], w[i]));
				g[v[i]].Add((u[i], w[i]));
			}
			var dist = new long[n][];
			for (int i = 0; i < n; i++)
			{
				dist[i] = Enumerable.Repeat(long.MaxValue / 3, 4).ToArray();
			}
			var q = new PriorityQueue<(long dist, int v, int state)>();
			q.Enqueue((0, 0, 0));
			dist[0][0] = 0;
			while (q.Count > 0)
			{
				var (d, cur, state) = q.Dequeue();
				if (dist[cur][state] < d) continue;
				foreach (var next in g[cur])
				{
					if (dist[next.to][state].Chmin(d + next.weight))
					{
						q.Enqueue((dist[next.to][state], next.to, state));
					}
					if (state == 0 || state == 1) if (dist[next.to][state + 2].Chmin(d))
						{
							q.Enqueue((dist[next.to][state + 2], next.to, state + 2));
						}
					if (state == 0 || state == 2) if (dist[next.to][state + 1].Chmin(d + next.weight * 2))
						{
							q.Enqueue((dist[next.to][state + 1], next.to, state + 1));
						}
					if (state == 0) if (dist[next.to][state + 3].Chmin(d + next.weight))
						{
							q.Enqueue((dist[next.to][state + 3], next.to, state + 3));
						}
				}
			}
			var ans = new List<long>();
			for (int i = 0; i < n - 1; i++)
			{
				ans.Add(dist[i + 1][3]);
			}
			Console.WriteLine(ans.Join(" "));
		}
```

</details>

ACコード: https://codeforces.com/contest/1473/submission/104360128  

## 感想

コンテストが終わった後の TL を見て、これは解けるべき問題だったなと感じました。Educational と名が付くだけあって、確かに教育的問題でしたね。「間違えてはならない問題」としての発想の引き出しの中身がまた一つ増えました。

超頂点という名前[^1]こそ付いていますが、「最短路問題は本質的に DP (動的計画法) と等価」ということを思い出せば、頂点倍化するタイプの超頂点構築は DP における状態を増やすテクニックと同じであると考えることが自然にできると思います。

[^1]: 超頂点という名前が好きです。「拡張 Dijkstra」という名前はあまりにも非本質的だと思います。
