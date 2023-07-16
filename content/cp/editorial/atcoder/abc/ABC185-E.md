---
title: "AtCoder Beginner Contest 185 E - Sequence Matching 解説"
date: 2020-12-14T01:20:27+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "やらかし反省録が増えました！！！！！！！！！！！！！！！！！！！ 問題リンク https://atcoder.jp/contests/abc185/tasks/abc185_e 問題概要 要素からなる整数列 があります。 からいくつかの要素をコスト で取り除くことができます。取り…"
aliases:
  - /entry/2020/12/14/012027
---

<!-- 解説ブログ テンプレ -->

やらかし反省録が増えました！！！！！！！！！！！！！！！！！！！

## 問題リンク

https://atcoder.jp/contests/abc185/tasks/abc185_e

## 問題概要

$N,\ M$ 要素からなる整数列 $A,\ B$ があります。$A,\ B$ からいくつかの要素をコスト $1$ で取り除くことができます。取り除いたあと、$A,\ B$ の要素数が同じでなくてはなりません。  
取り除いたあとの $A,\ B$ について $A\_i\neq B_i$ である要素があれば、その個数だけコストが $1$ ずつ上乗せされます。  
コストの最小値を求めてください。

## 制約

$1\le N,\ M\le 1000$  
$1\le A\_i,\ B\_i\le 10^{9}$  

<!--more-->

## 考察・解法

$A,\ B$ のうちそれぞれ $1$ から $i,\ j$ 要素目までを見たときのコストの最小値で DP を考えます。これを $dp_{i,\ j}$ と定義します。$i,\ j=0$ のときは、空の数列であるとします。  
DP なので、初期条件及び $i,\ j$ についての遷移が分かれば良いです。  

初期条件ですが、$i=0$ とすると、空の数列と $j$ 要素の数列におけるコストは $j$ なので、$dp_{0,\ j}=j$ です。$j=0$ のときも同様です。

では、$dp_{i,\ j}$ をそれまでに見た値で計算できないか考えます。  
以下のように $3$ パターンに分けて説明します。

- $A$ の最後の数を削除するとき  
コスト $1$ で可能です。よって、$dp\_{i,\ j}=dp_{i-1,\ j}+1$ です。  

- $B$ の最後の数を削除するとき  
同様に、$dp\_{i,\ j}=dp_{i,\ j-1}+1$ です。  

- $A,\ B$ の最後の数をどちらも残すとき  
この場合、$A\_i=B\_j$ であれば違う値であるときのコストは発生しないので、$dp\_{i,\ j}=dp_{i-1,\ j-1}$ です。  
そうでない場合は、違う値であるときのコストが発生します。$dp\_{i,\ j}=dp_{i-1,\ j-1}+1$ です。  
なお、$A\_i,\ B_j$ の両方を削除することは無意味です。両方残せば高々 $1$ の追加のコストであるからです。

以上の通りの DP をすればよいです。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = cin.ReadValue<int, int>();
			var a = cin.ReadIntArray(n);
			var b = cin.ReadIntArray(m);
			var dp = new int[n + 1][];
			for (int i = 0; i < n + 1; i++)
			{
				dp[i] = new int[m + 1];
			}
			for (int i = 0; i < n + 1; i++)
			{
				dp[i][0] = i;
			}
			for (int i = 0; i < m + 1; i++)
			{
				dp[0][i] = i;
			}
 
			for (int i = 1; i <= n; i++)
			{
				for (int j = 1; j <= m; j++)
				{
					dp[i][j] = dp[i - 1][j - 1] + 1;
					if (a[i - 1] == b[j - 1]) dp[i][j]--;
					dp[i][j].Chmin(dp[i][j - 1] + 1);
					dp[i][j].Chmin(dp[i - 1][j] + 1);
				}
			}
			Console.WriteLine(dp[n][m]);
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc185/submissions/18769369  

## 感想

コンテスト本番やらかし案件です。ABCDF の 5 完時点では 50 位橙パフォだったのに、E が解けずずるずると水パフォまで落ちてしまいました。うわああ……。

この問題は<b>編集距離</b>を求める DP そのものであり、典型です。典型を典型と見抜けなかったのもまずいのですが、この程度の DP を再発明できなかった (しかも過去に類似の DP である LCS の DP で痛い目に遭っているのに！) のは、正直に言って青コーダー失格レベルだと思います。大反省です。


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://fairy-lettuce.hatenadiary.com/entry/2020/11/16/043025"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


DP で頭が爆発することが多いし、今回も過去の教訓があったにも関わらずこれを通せなかったし、反省として、今週は DP 強化週間とします。EDPC 及び TDPC を全部埋める気でいきます。対戦よろしくお願いします。