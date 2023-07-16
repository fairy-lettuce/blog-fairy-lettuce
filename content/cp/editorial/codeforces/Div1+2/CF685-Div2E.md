---
title: "Codeforces Round #685 Div.2-E1/E2 - Bitwise Queries 解説"
date: 2020-11-22T04:28:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","Codeforces"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "数列を当てる系のインタラクティブとても好きだし得意。この前の JOI の数当てもそうだったけど、こういう問題を考えるのがとても楽しいと思えます。 問題リンク E1 (Easy Version): https://codeforces.com/contest/1451/proble…"
aliases:
  - /entry/2020/11/22/042800
---

数列を当てる系のインタラクティブとても好きだし得意。この前の JOI の数当てもそうだったけど、こういう問題を考えるのがとても楽しいと思えます。

## 問題リンク
E1 (Easy Version): https://codeforces.com/contest/1451/problem/E1  
E2 (Hard Version): https://codeforces.com/contest/1451/problem/E2

## 問題概要
$n$ 要素からなる数列 $a$ を当てるインタラクティブ問題。解答者はプログラムの中で以下の質問をすることができます。

- `AND i j`: $a\_{i}$ と $a_{j}$ の AND を質問する。
- `OR i j`: $a\_{i}$ と $a_{j}$ の OR を質問する。
- `XOR i j`: $a\_{i}$ と $a_{j}$ の XOR を質問する。

規定の回数以内の質問で元の数列を当ててください。

## 制約
$4\le n\le 2^{16}$  
$n$ は $2$ の冪である  
<b>Easy</b>: 質問の回数は $n+2$ 回以内  
<b>Hard</b>: 質問の回数は $n+1$ 回以内

<!--more-->

## 考察・解法
以下数列は $0$-indexed で表します。また、XOR を $\oplus$、AND を $\land$、OR を $\lor$ で表します。  

とりあえず制約の $2$ の冪ということは忘れておいて、数列の全ての数をどうしたら求められるか考えます。  
$a\_{0}$ が求まっていなくてもいいので $a\_{0}\oplus a\_{i}$ を求めると、後で $a_{0}$ が求まった際に $(a\_{0}\oplus a\_{i}) \oplus a\_{0} = a\_{i}$ から $a_{0}$ を復元することができるので都合がいいです。ということで、全体の方針としては

- $1\le i\le n-1$ を満たす全ての $i$ について、`XOR 0 i` を質問する。
- どうにかして $a_{0}$ を求める (Easy なら 3 回以内、Hard なら 2 回以内)。

とすればいいでしょう。

問題は $a_{0}$ を求めるパートです。XOR ではどう頑張っても特定できそうには無いことが容易に分かるので、OR か AND を上手く使ってみることにします。  
ここで、$a\_{0}\oplus a\_{i}\ (i=0,\ 1,\ \dots ,\ n-1)$ の数列の各要素を $b\_{i}$ としておくと、$b\_{i}$ は $a\_{0}$ の bit だけを反転させただけであり、<b>相対的には同じ</b>(すなわち、$a\_{i}=a\_{j} \iff b\_{i}=b_{j}$ が bit 毎に成り立つということ)であることに気付きます。ここで $b$ に現れる値に重複があるかどうかで場合分けをすることができると推測します[^1]。

- $b$ に重複した値が存在するとき  
重複した値を $b_i,\ b_j$ とします。このとき $a_i,\ a_j$ は同じ値であるため、$a\_{i}\land a\_{j}=a\_{i}\lor a\_{j}=a\_{i}(=a\_{j})$ です。これより、`AND i j` もしくは `OR i j` により値が具体的に求まったため、$b\_{i}\oplus a\_{i}=(a\_{0}\oplus a\_{i})\oplus a\_{i}=a_{0}$ として $a_0$ を求めることができます。
よって、質問は計 $n$ 回のみで答えを得られます。

- $b$ に重複した値が存在しないとき  
制約より、$a$ 及び $b$ は $[0,\ n-1$] の値を取ります。また、$n$ は $2$ の冪であることより、$a$ と $b$ の値は $0\cdots000\_{(2)},\ 0\cdots001\_{(2)},\ 0\cdots010\_{(2)},\ \dots ,\ 1\cdots111_{(2)}$ を全て取ることが分かります。  
ここで、$b$ を適当な値にして実験をしてみます。$a\_0\land a\_i=a\_0\land(a\_0\oplus b_i)$ を見てみることにしましょう。  
例えば $n=32=2^{5},\ a\_0=21=10101\_{(2)}$ とし、$b\_i=7=111_{(2)}$ を満たす $i$ で上の値を求めてみます。  
$a\_0\land a\_i\\\\=a\_0\land (a\_0\oplus b\_i)\\\\=10101\_{(2)}\land (10101\_{(2)}\oplus 111\_{(2)})\\\\=10101\_{(2)}\land 10010\_{(2)}=10000_{(2)}$  
これを見ると、$b\_i$ のうち <b>$1$ である bit は $a\_0$ の対応する bit に関わらず $0$、$0$ である bit は $a\_0$ の bit が反映される</b>ことが分かります。$b\_i$ の bit が $1$ だと XOR の反転によりかならず $a\_0,\ a\_0\oplus b_i$ の bit が逆になるためですね。より厳密に表すと、bitwise NOT を $\lnot$ で表せば  
$a\_0\land a\_i=a\_0\land(\lnot b_i)$  
であるということです。  
$b\_i$ は既に知っていて、$b\_0$ 以外は $1,\ 2,\ \dots ,\ n-1$ のどれかの値を取ることは分かっています。そこで、$b\_i\oplus b\_j=n-1=1\cdots 111\_{(2)}$ となるペア(例えば、$1,\ n-2(=1\cdots 110_{(2)})$)を持ってくると、  
$(a\_0\land a\_i)\oplus (a\_0\land a\_j)=a_0$  
となり、無事に $a_0$ が求まりました。質問は 2 回のみなので、計 $n+1$ 回で答えを得られます。

## 実装
$b$ をそのまま持つのと $b$ の値で逆引きするのとを併用したらいいと思います。あとは上に書いてあることを実装するだけです。  
ちなみに、質問をしてその結果を返すメソッドを作ってみたらとても便利でデバッグもしやすかった(特にジャッジにあたる部分を自分で実装する際に切り分けられるのが便利)のでおすすめです。
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public static void Solve(Scanner cin)
{
	var n = cin.ReadInt();

	var table = new Set<int>[n];
	var axor = new int[n];
	for (int i = 0; i < n; i++)
	{
		table[i] = new Set<int>();
	}
	table[0].Add(0);
	int two = -1;
	for (int i = 1; i < n; i++)
	{
		var ret = Ask("XOR", 0, i, cin);
		if (ret == -1) return;
		table[ret].Add(i);
		axor[i] = ret;
		if (table[ret].Count > 1) two = ret;
	}
	if(two != -1)
	{
		var and = Ask("AND", table[two][0], table[two][1], cin);
		if (and == -1) return;
		var ans = new int[n];
		ans[0] = two ^ and;
		for (int i = 1; i < n; i++)
		{
			ans[i] = ans[0] ^ axor[i];
		}
		Console.WriteLine($"! {ans.Join(" ")}");
	}
	else
	{
		var one = Ask("AND", 0, table[1][0], cin);
		var notone = Ask("AND", 0, table[(n - 1) ^ 1][0], cin);
		var ans = new int[n];
		ans[0] = one ^ notone;
		for (int i = 1; i < n; i++)
		{
			ans[i] = ans[0] ^ axor[i];
		}
		Console.WriteLine($"! {ans.Join(" ")}");
	}
}

static int Ask(string q, int i, int j, Scanner cin)
{
	Console.WriteLine($"{q} {i + 1} {j + 1}");
	Console.Out.Flush();
	var ret = cin.ReadInt();
	return ret;
}
```

</details>

ACコード: https://codeforces.com/contest/1451/submission/99183661  

## 感想
数当てインタラクティブしか勝たんになりました。楽しかったです。  
あと、この問題のおかげで薄橙になったので嬉しいです[^2]。  
追記: CF-predictor がバグっていたようで、<b>色変には届きませんでした</b>。


<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/fairly_lettuce/status/1330236559560503298"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<!--more-->

<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/fairly_lettuce/status/1330236684143878144"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


おこです。


[^1]: 私は色々な $a$ を作っては $b$ がどうなるかを眺めていると、$a$ に同じ要素があるときは $b$ で検出できるし、しかも $a\_i,\ a_j$ は AND によって具体的に求められることに気付きました。しかし、制約を見ると順列になる場合は上手く行かなさそうだったので場合分けを考える、という思考になりました。
[^2]: 色変記事はアドベントカレンダーの際にまとめて書きます。