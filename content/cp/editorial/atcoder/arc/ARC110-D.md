---
title: "鹿島建設プログラミングコンテスト2020 (ARC110) D - Binomial Coefficient is Fun 解説"
date: 2020-12-06T01:03:56+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "数学問エスパーが成功したときほど気持ちいい瞬間はありません。 問題リンク https://atcoder.jp/contests/arc110/tasks/arc110_d 問題概要 長さ の非負整数列 があります。長さ 、総和 以下の非負整数列 全てについて、 の総和を で割っ…"
aliases:
  - /entry/2020/12/06/010356
---

<!-- 解説ブログ テンプレ -->

数学問エスパーが成功したときほど気持ちいい瞬間はありません。

## 問題リンク

https://atcoder.jp/contests/arc110/tasks/arc110_d

## 問題概要

長さ $N$ の非負整数列 $A$ があります。長さ $N$、総和 $M$ 以下の非負整数列 $B$ 全てについて、$\displaystyle \prod_{i=1}^{N} \binom{B\_i}{A\_i}$ の総和を $10^9+7$ で割った余りを出力してください。

## 制約

$1\le N\le 2000$  
$1\le M\le 10^9$  
$1\le A_i \le 2000$  

<!--more-->

## 考察・解法

直接数え上げは頭が混乱しそうなので、<b>形式的冪級数</b>を用いた考察をしてみます。  

以下、$S=\sum\_{i=0}\^{N-1}A\_i,\ m=M-S=M-\sum A_i$ とします。求めるものは、各二項係数に $m$ を分配したものと考えられます。各二項係数は互いに区別ができるので、$m$ の<b>分配のしかた</b>は重複組合せで表せると分かります。  
ところで、$m$ 個のものを $N$ 個の箱に分配する方法の通り数は、形式的冪級数を用いて以下のように表せます。  

<div>$\displaystyle [x^m] \prod_{i=0}^{N-1}(1+x+x^2+\dots)$</div>

これは $i$ 番目の箱に $x$ の次数個のものを入れる場合の数と解釈できます。  
では、$i$ 番目の箱にものをある個数入れたときの答えへの寄与を $1$ ではなく、二項係数にしましょう。すると、次のように表されます。  

<div>$\displaystyle [x^m] \prod_{i=0}^{N-1}\left(\binom{A_i}{A_i}+\binom{A_i+1}{A_i}x+\binom{A_i+2}{A_i}x^2+\dots\right)=[x^m] \prod_{i=0}^{N-1} \sum_{j=0}^{\infty}\binom{A_i+j}{A_i}x^j$</div>

これを簡単な式で表せることができたら答えです。以下、  

<div>$\displaystyle f(x,\ A_i)=\sum_{j=0}^{\infty}\binom{A_i+j}{A_i}x^j$</div>

と表すことにします。  

さて、これに関して簡単に実験をすると、次のような式が成立するのではないかと予想することができます[^1]。

<div>$f(x,\ p)f(x,\ q)=f(x,\ p+q+1)$</div>

証明は略しますが難しくはないと思います。  
これより、求めたい形式的冪級数は次のように簡単に表されることが分かります。

<div>$\displaystyle \prod_{i=0}^{N-1} \sum_{j=0}^{\infty}\binom{A_i+j}{A_i}x^j=\prod_{i=0}^{N-1}f(x,\ A_i)=f\left(x,\ \sum_{i=0}^{N-1}A_i+N-1\right)$</div>

答えはこの形式的冪級数の $x^i\ (i=0,\ 1,\ \dots,\ m)$ の係数の総和なので、次のようにひとつの二項係数で表されます。

<div>$\displaystyle \sum_{k=0}^{m} [x^k] f\left(x,\ \sum_{i=0}^{N-1}A_i+N-1\right)=\sum_{i=0}^{m}\binom{i+S+N-1}{S+N-1}=\binom{S+N+m}{S+N}=\binom{M+N}{S+N}$</div>

## 実装
$S+N\le 2000^2+2000$ より、これは二項係数の定義通り計算できます。  

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, m) = cin.ReadValue<int, long>();
			var a = cin.ReadLongArray(n);
			m -= a.Sum();
			if(m < 0)
			{
				Console.WriteLine(0);
				return;
			}
 
			long k = a.Sum() + n - 1;
			ModInt ans = 1;
 
			for (int i = 1; i <= k + 1; i++)
			{
				ans *= (m + i);
				ans /= i;
			}
 
			Console.WriteLine(ans);
		}
```

</details>

ACコード: https://atcoder.jp/contests/arc110/submissions/18591839  

## 感想

前回の ARC109-E で実験エスパーが話題になりました。その反省より実験して法則性を見出すのに敏感になっていたのが幸いしました。  

また、ちょっと前に maspy さんの形式的冪級数解説記事をざっと読んでどういうものか理解していました。形式的冪級数を考察の手段として使う方法をこの記事で学び、それが早速今回役立ちました。素晴らしい記事をありがとうございます。


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://maspypy.com/category/%e5%bd%a2%e5%bc%8f%e7%9a%84%e3%81%b9%e3%81%8d%e7%b4%9a%e6%95%b0%e8%a7%a3%e8%aa%ac"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


【追記】なお、今回の問題で扱っている内容は「負の二項定理」そのものになります。


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://maspypy.com/%e5%a4%9a%e9%a0%85%e5%bc%8f%e3%83%bb%e5%bd%a2%e5%bc%8f%e7%9a%84%e3%81%b9%e3%81%8d%e7%b4%9a%e6%95%b0%ef%bc%88%ef%bc%93%ef%bc%89%e7%b7%9a%e5%bd%a2%e6%bc%b8%e5%8c%96%e5%bc%8f%e3%81%a8%e5%bd%a2%e5%bc%8f#toc4"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


ちなみに、今回のコンテストで無事 AtCoder 青色になれました。色変記事は 12/9 の色変記事アドベントカレンダーにて公開予定です。お楽しみに！

[^1]: $A\_i=1,\ 2$ などのケースで実際に計算すると分かりやすいかもしれません。私はサンプルケース 1 ($A=(1,\ 2,\ 1)$) に関して実験したところ、求めたい冪級数が $1+7x+28x\^2+84x^3+\dots$ となり、係数に $\binom{i+6}{6}$ が現れたことから予測がつきました。