---
title: "JOI 2021 二次予選 C イベント巡り (Event Hopping)"
date: 2020-12-16T05:37:58+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","JOI"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "JOI 2021 二次予選をバチャしました。難しかったし、納得いく結果が出せませんでしたが、面白い問題でした！ 問題リンク https://atcoder.jp/contests/joi2021yo2/tasks/joi2021_yo2_c 問題概要 つの街があり、合計 個のイベ…"
aliases:
  - /entry/2020/12/16/053758
---

<!-- 解説ブログ テンプレ -->

JOI 2021 二次予選をバチャしました。難しかったし、納得いく結果が出せませんでしたが、面白い問題でした！

## 問題リンク

https://atcoder.jp/contests/joi2021yo2/tasks/joi2021_yo2_c

## 問題概要

$2$ つの街があり、合計 $N$ 個のイベントが行われます。イベント $i$ は街 $P\_i$ で開催され、時間は $(S\_i,\ S_i+1)$ の間行われます。  

JOI 君はこれらのイベントに参加します。どちらの街から始めることができ、それまでに参加したイベント数を $j$ 個とすると、$D+K\times j$ だけ街の間の移動に時間がかかります。  

参加できるイベント数の最大値を求めてください。

## 制約 (満点)

$1\le N\le 200\ 000$  
$1\le D\le 10^{12}$  
$0\le K\le 10^{12}$  
$1\le P_{i} \le 2$  
$1\le S\_{i} \le 10^{12}$  
$S_{i}$ は全て異なる。  

<!--more-->

## 考察・解法

以下、$1$-indexed とします。それぞれのイベントについて DP をするとよいのではないかと分かります。  
$i$ 番目のイベントまでに参加したときに、最後に街 $j$ にいる状態でのイベント参加数の最大値を $dp_{i,\ j}$ とします。  

まず、部分点である $K=0$ のときについては、$i$ 番目のイベント開始時 (時刻 $S\_i$) に間に合うように移動するためには、時刻 $S_i-D-1$ よりも後に別の街で開かれるイベントは参加できません。よって、それ以前に開かれるイベントの番号を二分探索で調べれば遷移できます。

では、$K\neq 0$ のときはどうすればいいでしょうか。  
イベントに参加した後に移動するときは、$D+1$ 以上前の最後のイベント開始時から遷移するだけでは不十分です。また、最後のイベントから遷移するけど途中のイベントに意図的に不参加する、という遷移では間違いです (例えば、$(D,\ K)=(2,\ 3)$ で時刻 $1,\ 2,\ 12$ に街 $1$ で、$14$ に街 $2$ でイベントがあるときは時刻 $2$ のイベントから遷移しなければいけません)。また、当然それまでのイベントからすべて遷移しては、$O(N^2)$ かかるので間に合いません。

それでは、それまでのイベント参加数が $j$ であるときにそこから違う街で行われる $i$ 番目のイベントに参加するためにはどのような条件が必要でしょうか。以下、説明のために街 $1$ から街 $2$ へ移動することを考えます。  
それまでのイベント参加数が $j$ 以上である時刻から移動するとしたとき、街 $1$ では長居する必要はなく、イベント参加数 (すなわち $dp$ の値) が $j$ 以上となる最初のイベント ($s$ 番目であるとします) から動けばよいです。このとき、$S\_s\le S_i-(D+K\times j)-1$ であるとき、街 $1$ から街 $2$ に移動することは可能です。  
ところで、$S\_s+(D+K\times j)$ は $j$ について<b>単調増加</b>です。$S_s$ は街 $1$ にいて、それまでに $j$ 個のイベントに参加できるイベントの最小値なので $j$ について単調増加だからです。これより、街 $2$ に $j$ 個イベントに参加した状態で移動できる最大の $j$ は二分探索できます。  

よって、以上より $O(N\log N)$ で DP 遷移をすることができます。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, d, k) = cin.ReadValue<int, long, long>();
			var (p, s) = cin.ReadValueArray<int, long>(n);
			var ev = Enumerable.Zip(p, s).OrderBy(v => v.Second).ToArray();
			var sortedS = ev.Select(p => p.Second).ToArray();
			var dp = new long[n + 1, 2];
			if (k == 0)
			{
				for (var i = 0; i < n; i++)
				{
					dp[i + 1, 0] = dp[i, 0];
					dp[i + 1, 1] = dp[i, 1];
					var before = sortedS.UpperBound(sortedS[i] - d - 1) - 1;
					if (ev[i].First == 1)
					{
						dp[i + 1, 0] = dp[i, 0] + 1;
						if (before >= 0) dp[i + 1, 0].Chmax(dp[before + 1, 1] + 1);
					}
					else
					{
						dp[i + 1, 1] = dp[i, 1] + 1;
						if (before >= 0) dp[i + 1, 1].Chmax(dp[before + 1, 0] + 1);
					}
				}
				Console.WriteLine(Max(dp[n, 0], dp[n, 1]));
			}
			else
			{
				var firstapp1 = Enumerable.Repeat(int.MaxValue, n + 1).ToArray();
				var firstapp2 = Enumerable.Repeat(int.MaxValue, n + 1).ToArray();
				var unseen1 = 1;
				var unseen2 = 1;
				firstapp1[0] = 0;
				firstapp2[0] = 0;
				for (var i = 0; i < n; i++)
				{
					dp[i + 1, 0] = dp[i, 0];
					dp[i + 1, 1] = dp[i, 1];
					if (ev[i].First == 1)
					{
						dp[i + 1, 0] = dp[i, 0] + 1;
 
						var ok = 0;
						var ng = unseen2;
						while (ng - ok > 1)
						{
							var mid = (ng - ok) / 2 + ok;
							var t = sortedS[i] - sortedS[firstapp2[mid] - 1] - 1;
							if (d + k * mid <= t) ok = mid;
							else ng = mid;
						}
 
						dp[i + 1, 0].Chmax(ok + 1);
					}
					else
					{
						dp[i + 1, 1] = dp[i, 1] + 1;
 
						var ok = 0;
						var ng = unseen1;
						while (ng - ok > 1)
						{
							var mid = (ng - ok) / 2 + ok;
							var t = sortedS[i] - sortedS[firstapp1[mid] - 1] - 1;
							if (d + k * mid <= t) ok = mid;
							else ng = mid;
						}
 
						dp[i + 1, 1].Chmax(ok + 1);
					}
					for (var j = unseen1; j <= dp[i + 1, 0]; j++)
					{
						firstapp1[j].Chmin(i + 1);
					}
					unseen1 = (int)dp[i + 1, 0] + 1;
					for (var j = unseen2; j <= dp[i + 1, 1]; j++)
					{
						firstapp2[j].Chmin(i + 1);
					}
					unseen2 = (int)dp[i + 1, 1] + 1;
				}
				Console.WriteLine(Max(dp[n, 0], dp[n, 1]));
			}
		}
```

</details>

ACコード: https://atcoder.jp/contests/joi2021yo2/submissions/18798740  

## 感想

バチャではこれと A 問題の 2 完と部分点で 225 点でした。3 完はしたかったですが……。  

もっと JOI もたくさん問題を解きたいですね。もう大学生なので JOI には参加できないですが…… (中高生の頃から競プロを知りたかった……)。
