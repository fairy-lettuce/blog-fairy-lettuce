---
title: "Google Code Jam Round 1C 2022 B - Squary 解説"
date: 2022-04-30T21:23:14+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","GCJ"]
tags: ["Competitive", ]
archives: ["2022", "2022-04"]
description: "おもしろかった。気付いたときうわーーーーって言いました。 問題リンク https://codingcompetitions.withgoogle.com/codejam/round/0000000000877b42/0000000000afdf76 問題概要 数列 に、 個以下の…"
aliases:
  - /entry/2022/04/30/212314
---

<!-- 解説ブログ テンプレ -->

おもしろかった。気付いたときうわーーーーって言いました。

## 問題リンク

https://codingcompetitions.withgoogle.com/codejam/round/0000000000877b42/0000000000afdf76

**2023/07/16 追記**

GCJ のサイトが閉じられたので、代替となるリンクを貼ります。

https://github.com/google/coding-competitions-archive/tree/main/codejam/2022/round_1c/squary

なお、GCJ の過去問のデータは[このリポジトリ](https://github.com/google/coding-competitions-archive)で参照できます。

## 問題概要

数列 $\{E_i\}$ に、$K$ 個以下の整数 ($-10^{18}$ 以上 $10^{18}$ 以下) を追加し、以下の等式が成り立つようにしてください。

- $\displaystyle \sum E_i\^2=\left(\sum E_i \right)^2$  

## 制約

- $1\le T\le 100$ (テストケース数)  
- $1\le N\le 1000$ (数列の長さ)  
- $-1000\le E_i\le 1000$  

## 小課題

- 小課題 1 (9pts): $K=1$  
- 小課題 2 (22pts): $2\le K \le 1000$

<!--more-->

## 考察・解法

### 小課題 1

追加する数を $X$ とします。このとき、

$\displaystyle \sum E_i\^2+X\^2=\left(\sum E_i+X \right)^2$

ここで、$\displaystyle A:=\sum E_i^2, B:=\sum E_i$ とおくと、

$\displaystyle 2BX=A -B^2$

$\displaystyle \therefore X=\frac{A-B^2}{2B}$  

これが整数になれば良いです。

コーナーケースですが、

- $B=0$ のとき:  
    $A=0$ (つまり、全ての $E_i=0$ ) ならば何を出力しても良い。  
    そうでないならば、$K=1$ では不可能。

には気をつけましょう。

### 小課題 2

追加する数を $2$ つに増やし、$X, Y$ として、$X, Y$ の満たすべき条件を求めます。

このとき、どのような数列でも実は適当な $X, Y$ を取ることができます。

与えられた条件は、$A(=\sum E_i^2),B(=\sum E_i),X,Y$ について表すと、

$\displaystyle A+X\^2+Y\^2=(B+X+Y)^2$

$\displaystyle \therefore A=B^2+2XY+2B(X+Y)$

となります。これでは $X,Y$ について分かりにくい式なので、$X,Y$ が $1$ つの項にまとまるようにくくります。

$\displaystyle 2(X+B)(Y+B)=A+B^2$

ところで、$A(=\sum E_i\^2),B(=\sum E_i)$ の偶奇は一致する (証明略) ので、右辺は偶数です。よって、$(X+B)(Y+B)=(A+B^2)/2$ は整数となります (**必要条件**)。

$(A+B\^2)/2$ の絶対値は $10^{12}$ を超えないので、適当に割り振っても良いです。

例えば、$(X+B,Y+B)=((A+B\^2)/2, 1) \iff (X,Y)=((A+B^2)/2-B, 1-B)$ とすればどのようなケースでも条件を満たす解になります。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve(int testcase)
		{
			var n = sr.ReadInt();
			var k = sr.ReadInt();
			var a = sr.ReadLongArray(n);
			var sum = a.Sum();
			var squareSum = a.Select(i => i * i).Sum();
			WriteCase(testcase);
			if (squareSum == 0 && sum == 0)
			{
				Console.WriteLine(0);
			}
			else if (sum == 0 || (squareSum - sum * sum) % (2 * sum) != 0)
			{
				if (k == 1) Console.WriteLine("IMPOSSIBLE");
				else
				{
					var p = (squareSum + sum * sum) / 2;
					// solve for x, y s.t. (x+sum)(y+sum) == p
					// x = 1-sum, y = p-sum
					var x = 1 - sum;
					var y = p - sum;
					Console.WriteLine($"{x} {y}");
				}
			}
			else
			{
				Console.WriteLine((squareSum - sum * sum) / (2 * sum));
			}
		}

		public void WriteCase(int testcase)
		{
			Console.Write($"Case #{testcase + 1}: ");
		}
```

</details>

ACコード: 省略 (GCJ の提出コード共有するの面倒なので……)

## 感想

GCJ っぽい (本当に？) ギャグ問題でした。ギャグだけど、実は $K\ge 2$ のときは $K=2$ で全部達成できる、みたいな考え方はよく見るかもしれない……。

解けたら気持ちいいけれど解けなかったらただただ不快になりそう……。
