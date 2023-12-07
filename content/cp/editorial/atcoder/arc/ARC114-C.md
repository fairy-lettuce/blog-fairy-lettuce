---
title: "AtCoder Regular Contest 114 C - Sequence Scores 解説"
date: 2021-03-19T07:42:33+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-03"]
description: "解説記事を書くのはかなりお久しぶりになってしまいました。 問題リンク https://atcoder.jp/contests/arc114/tasks/arc114_c 問題概要 以上 以下の整数からなる長さ の数列 に対して、 を以下のように定義します。 長さ の数列 があり、…"
aliases:
  - /entry/ARC114-C
---

<!-- 解説ブログ テンプレ -->

解説記事を書くのはかなりお久しぶりになってしまいました。

## 問題リンク

https://atcoder.jp/contests/arc114/tasks/arc114_c

## 問題概要

$1$ 以上 $M$ 以下の整数からなる長さ $N$ の数列 $A$ に対して、$f(A)$ を以下のように定義します。

- 長さ $N$ の数列 $X$ があり、初め全ての要素は $0$ である。$f(A)$ を、次の操作を繰り返して $X$ を $A$ に等しくするために必要な最小の操作回数とする。

    - $1\le l\le r\le N$ と $1\le v\le M$ を指定する。$1\le i\le r$ に対して $X\_i$ を $\max(X_i,\ v)$ で置き換える。

全ての $A$ に対する $f(A)$ の和を $\bmod 998244353$ で求めてください。

## 制約

- $1\le N,\ M\le 5000$

<!--more-->

## 考察・解法

一見すると DP で解けそうに見えますが、直接数え上げが出来そうな雰囲気もします。DP は色々考えてもほとんど思い浮かばないので思い切って直接数え上げる方針でいきます。

最初に $f(A)$ を言い換えます。数列 $A$ のうち以下の条件を満たす要素 $A_i$ の個数を $s$ とすると、$f(A)=N-s$ です。

- ある $0\le j\lt i$ が存在し $A\_j=A\_i$ を満たす。また、この $j$ について、全ての $j\le k\lt i$ について $A\_k \ge A_i$ を満たす。…… [条件 ★]

これを満たす $A\_j$ があるならば、$l=j,\ r=i,\ v=A\_i$ として問題文の操作を行えば $A\_i$ のぶんの操作を $A_j$ に押し付けることができます。そうでない要素については操作を行わないといけないので、そのような要素の個数を数え上げます。

さて、$A\_i=a$ とします。$A\_i=a$ を満たす数列 $A$ は $M^{N-1}$ 通りありますが、その中で [条件 ★] を満たすものの個数を数え上げます。

[条件 ★] の $A\_j$ はある $A\_i$ について複数個存在するかもしれませんが、その中でも右端のものは一意に定まります。よって $A_j$ を固定して、

- $A\_j=A\_i=a$ かつ、全ての $j\lt k\lt i$ について $A_k\gt a$ を満たしている。

という条件を満たす数列 $A$ の個数を数え上げればよいです。これは、

$(M-a)\^{i-j-1}\times M^{N-(i-j-1)-2}$

個です。ただし、$a=M$ となる場合のみ $j=i-1$ でないとならないので、$i=0$ のとき $0$ 個、そうでないとき $M\^{N-2}$ 個となります (これは $0^0=1$ と定義すると元の式と一致します)。

よって、$A_i=a$ のときに [条件 ★] を満たす数列の個数は、($a\lt M$ であるとき)

$\displaystyle \sum\_{j=0}\^{i-1} (M-a)\^{i-j-1}\times M^{N-(i-j-1)-2}$

$\displaystyle =M\^{N-2}\sum\_{j=0}\^{i-1}\left(\frac{M-a}{M}\right)^{i-j-1}$

$\displaystyle =M\^{N-2}\sum\_{j=0}\^{i-1}\left(\frac{M-a}{M}\right)^j$

$\displaystyle =M\^{N-2}\frac{\left(\frac{M-a}{M}\right)^i-1}{\left(\frac{M-a}{M}\right)-1}$

$\displaystyle =M\^{N-2}\frac{\left(\frac{M-a}{M}\right)^i-1}{-\frac{a}{M}}$

$\displaystyle =M\^{N-i-1}\frac{M\^i-(M-a)^i}{a}$

です。ちなみにこの変形は Wolfram|Alpha に投げても得られます。

$a=M$ とすると、$i\gt 0$ のとき

$\displaystyle M\^{N-i-1}\frac{M\^i-(M-a)\^i}{a}=M^{N-2}$

、また $i=0$ のときも $0^0=1$ と定義すれば $0$ になることより、$a=M$ となる場合についてもこの式が成り立っていることが分かります。

この式に現れる変数は $i,\ a$ であり、それぞれ $0\le i\lt N,\ 1\le a\le M$ を独立に動きます。

$O(NM)$ はギリギリ間に合いそうなので、$i,\ a$ それぞれについてこの式の値を求め、その総和を求めればよいです。

## 実装

この問題では制約が大きめであり、$O(NM)$ に何かしらの $\log$ がつくと実行時間制限に間に合わない可能性が高いです。

この問題で $\log$ が付きそうな計算といえば、$\bmod 998244353$ 上の累乗計算および除算です。これらは以下のようにして $\log$ を無視可能な実装にできます。

- 累乗計算: 前計算する。底・指数それぞれ $5000$ 以下について前計算すれば十分。
- 除算: `for` 文を回す順番を $i,\ a$ の順番とし、$a$ で除算を行う項全ての総和をまとめて $1$ 回で除算する。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		ModInt[][] pow;

		public void Init()
		{
			pow = new ModInt[5005][];
			for (int i = 0; i < 5005; i++)
			{
				pow[i] = new ModInt[5005];
				pow[i][0] = 1;
				for (int j = 1; j < 5005; j++)
				{
					pow[i][j] = pow[i][j - 1] * i;
				}
			}
		}
 
		public void Solve()
		{
			Init();
			var (n, m) = sr.ReadValue<int, int>();
			ModInt ans = 0;
			for (int j = 1; j <= m; j++)
			{
				ModInt sum = 0;
				for (int i = 0; i < n; i++)
				{
					//ans += ((ModInt.Pow(m, i) - ModInt.Pow(m - j, i)) / j) * ModInt.Pow(m, n - i - 1);
					sum += ((pow[m][i] - pow[m - j][i])) * pow[m][n - i - 1];
				}
				sum /= j;
				ans += sum;
			}
			//Console.WriteLine(ModInt.Pow(m, n) * n - ans);
			Console.WriteLine(pow[m][n] * n - ans);
		}
```

</details>

ACコード: https://atcoder.jp/contests/arc114/submissions/20947519  

## 感想

数学ゲーだったので数式しかない文章になっちゃいました。読めるかな。

コンテスト中、定数倍で間に合わないの！？と思って焦って C++ を久々に書きました。しかし $\log$ を付けていると C++ でも通らず、$O(NM \log p)$ では通らないのではないかと思い始め更に総和を取る方向へと走ってしまいました[^1]。

結局 $\log$ を頑張って無くせば C++ はおろか C# でも AC できたというオチでした。解法は何もかも合っていたのにこのサボりのせいでコンテスト中の AC を逃し、冷えてしまいました。

ということで、改めて <b>$\log$ が落とせそうなときはサボらずちゃんと $\log$ を落とす</b>ことの大切さを学びました。<s>さては財宝やらなんやらで定数倍に苦しめられたとき結局 $\log$ が悪さしてたときに何も学んでないな？？？</s>

ということで、今回の反省文でした。次回の反省文もご期待ください。<s>次はこういうポカじゃないといいな……</s>

[^1]: 実装が下手で無限にバグらせていたので私が悪いのですが、ちゃんと総和を取れば $O(M\log N)$ でできますね。maspy さんの解説にもあります。