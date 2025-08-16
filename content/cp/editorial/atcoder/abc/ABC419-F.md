---
title: 'AtCoder Beginner Contest 419 F - All Included 解説'
date: 2025-08-17T00:36:00+09:00
draft: false
author: ['fairy_lettuce']
categories: ['競プロ解説', 'AtCoder']
tags: ['Competitive']
archives: ['2025', '2025-08']
aliases:
  - /entry/2025/08/17/036000
---

## 問題リンク

https://atcoder.jp/contests/abc419/tasks/abc419_f

## 問題概要

長さ $L$ の英小文字列であって、$S_1,S_2,\dots,S_N$ をすべて部分文字列として含むものの個数を数え上げよ。

## 制約

- $1\le N\le 8$
- $1\le L\le 100$
- $N,L$ は整数
- $1\le |S_i|\le 10$, 英小文字からなる文字列
- $S_i\ne S_j\ (i\ne j)$

<!-- more -->

## 解法

素直に DP する。ここで、各文字列が既に部分文字列として含まれているか ($O(2^N)$ 通り)、およびその文字列の suffix が重なる最長の prefix が状態になる。各文字列の prefix は $M=\sum_i |S_i|$ 個で、DP は全体で $O(2^NML\sigma)$ で動く (ただし $\sigma=26$)。

## 別解

公式解説では prefix の集合を考えていたが、この解法ではそれぞれの文字列を独立に考えている。

各文字列に対して、次のように定義される状態値 $a_i$ を定義する:

> 今の文字列の suffix は各文字列の prefix と最長何文字重なるか？

ただし、番兵として、既に部分文字列として含まれている場合は $a_i=|S_i|$ とかにしておく。

たとえば、今見ている文字列が `abcdce` であり $S_1$ が `dceff` なら `dce` が重なっているので $a_1=3$ である。
$S_2$ が `xyzw` のように重なりがなければ $a_2=0$ である。
$S_3$ が `bcd` のように既に部分文字列として含まれている場合 $a_3=|S_3|=3$ である。

すると、次どの文字が来るとどの状態に遷移するかは前計算できる。公式解法では DP 中に Aho-Corasick 法を使うとしていたが、この前計算は愚直にやっても $O(M\sigma|S_i|^2)$ で計算できる。

【追記】発想は Aho-Corasick 法と全く同じでした。車輪の再発明！【追記終わり】

各文字列について $0\le a_i\le|S_i|$ なので、全部で $\prod_i (|S_i|+1)\ (\le 2.15\times10^8)$ 通り。状態数の上界は 32 bit 整数型に収まる。そのため適当にハッシュ値を取って連想配列で管理できる。自分は $\sum_{i=0}^{N-1} a_i\times 11^i$ をハッシュ値とした。

すべての状態を取るわけではなく、$a_i\lt |S_i|$ であるときは prefix に対応する個数しか取り得ないので (このあたりは公式解説と同じ)、実際に考慮すべき状態数は $O(2^NM)$ 個しかない。

計算量は $O(M\sigma|S_i|^2+2^NML\sigma)$。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public override void Solve()
		{
			var mult = new long[11];
			mult[0] = 1;
			for (int i = 1; i < 11; i++)
			{
				mult[i] = mult[i - 1] * 11;
			}
			var (n, l) = sr.ReadValue<int, int>();
			var s = sr.ReadStringArray(n);
			var nd = Enumerable.Range(0, n).Select(p => Enumerable.Range(0, s[p].Length + 1).Select(q => new int[26]).ToArray()).ToArray();
			for (int i = 0; i < n; i++)
			{
				for (int j = 0; j < s[i].Length + 1; j++)
				{
					for (int k = 0; k < 26; k++)
					{
						if (j == s[i].Length)
						{
							nd[i][j][k] = s[i].Length;
							continue;
						}
						var next = s[i].Take(j).Concat(new[] { (char)('a' + k) }).Join();
						for (int p = j + 1; p >= 0; p--)
						{
							var s0 = s[i].Take(p).Join();
							var s1 = next.TakeLast(p).Join();
							if (s0 == s1)
							{
								nd[i][j][k] = p;
								break;
							}
						}
					}
				}
			}

			var dp = CreateArray(l + 1, i => new Dictionary<long, ModInt>());
			dp[0].Add(0, 1);
			for (int i = 0; i < l; i++)
			{
				foreach (var (hash, value) in dp[i])
				{
					var state = FromHash(n, hash, mult);
					for (int j = 0; j < 26; j++)
					{
						var nextHash = 0L;
						for (int k = 0; k < n; k++)
						{
							nextHash += nd[k][state[k]][j] * mult[k];
						}
						if (dp[i + 1].ContainsKey(nextHash))
							dp[i + 1][nextHash] += value;
						else dp[i + 1].Add(nextHash, value);
					}
				}
			}
			var goal = 0L;
			for (int i = 0; i < n; i++)
			{
				goal += s[i].Length * mult[i];
			}
			var ans = new ModInt(0);
			dp[l].TryGetValue(goal, out ans);
			sw.WriteLine(ans);
		}

		public long[] FromHash(int n, long h, long[] mult)
		{
			var ret = new long[n];
			for (int i = 0; i < n; i++)
			{
				var x = h / mult[i] % 11;
				ret[i] = x;
			}
			return ret;
		}

		public long ToHash(long[] x, long[] mult)
		{
			var ret = 0L;
			for (int i = 0; i < x.Length; i++)
			{
				ret += x[i] * mult[i];
			}
			return ret;
		}
```

</details>

AC コード: https://atcoder.jp/contests/abc419/submissions/68578129

## 感想

うにょーん
