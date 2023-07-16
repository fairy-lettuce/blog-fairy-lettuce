---
title: "AtCoder Beginner Contest 184 C - Super Ryuma 解説"
date: 2020-11-23T00:06:44+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "この問題がこのセットで最難……。 問題リンク https://atcoder.jp/contests/abc184/tasks/abc184_c 問題概要 斜めにどこまででも進め、また マンハッタン距離 が 以下である場所に進める駒「超竜馬」があります。 から へ動くのに必要な最…"
aliases:
  - /entry/2020/11/23/000644
---

この問題がこのセットで最難……。

## 問題リンク
https://atcoder.jp/contests/abc184/tasks/abc184_c

## 問題概要
斜めにどこまででも進め、また [マンハッタン距離](https://ja.wikipedia.org/wiki/%E3%83%9E%E3%83%B3%E3%83%8F%E3%83%83%E3%82%BF%E3%83%B3%E8%B7%9D%E9%9B%A2) が $3$ 以下である場所に進める駒「超竜馬」があります。  
$(r\_{1},\ c\_{1})$ から $(r\_{2},\ c_{2})$ へ動くのに必要な最小手数を求めてください。

## 制約
$1\le r\_{1},\ c\_{1},\ r\_{2},\ c_{2}\le 10^{9}$

<!--more-->

## 考察
$r,\ c$ をそのまま扱ってもいいですが、マンハッタン距離の典型として「<b>盤面を $45^\circ$ 回転する</b>」というものがあります。これにより、マンハッタン距離が[チェビシェフ距離](https://ja.wikipedia.org/wiki/%E3%83%81%E3%82%A7%E3%83%93%E3%82%B7%E3%82%A7%E3%83%95%E8%B7%9D%E9%9B%A2)になり計算が簡単になるというものです。今回もそれで考察が簡単になるので回転してみます。  
$x=r+c,\ y=r-c$ とおくと、一回で移動できる場所は次の図のようになります。

<center>
<a href="https://f.hatena.ne.jp/fairy_lettuce/20201123000525">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/f/fairy_lettuce/20201123/20201123000525.jpg" alt="20201123000525">
</a></center>

これを見ると、$1$ 回で移動できる条件は次のいずれかを満たすときと分かります。

- $\max(x,\ y)\le 3$
- $x=0$
- $y=0$

これを元に、何回で移動できるかの条件を書き出します。

## 解法
上から順に条件を見ていって初めて合致したときの回数が答えです。  

- $0$ 回で移動できる必要十分条件  
二点の座標が同じであるときです。


- $1$ 回で移動できる必要十分条件  
先程の考察にある通りです。次のうちのいずれかを満たすときです。  
    - $\max(x,\ y)\le 3$
    - $x=0$
    - $y=0$


- $2$ 回で移動できる必要十分条件  
ちょっと難しいですが、$x,\ y$ 軸に並行に移動する回数とチェビシェフ距離が $3$ 以下の場所に移動する回数で場合分けします。
    - $2$ 回 $x,\ y$ 軸に並行に移動するとき  
同じ軸に並行に移動しても仕方ないので、$x$ 軸に並行に移動したのち $y$ 軸に並行に移動します。$x,\ y$ 軸に並行に移動するときは偶数マスずつしか進めないことが図より分かるので、二点の $x,\ y$ 座標の差がどちらも偶数のとき移動できます。  
    - $x,\ y$ 軸に並行に移動したのちチェビシェフ距離が $3$ 以下の場所に移動するとき  
$x$ 座標の差と $y$ 座標の差のどちらかが $3$ 以下のとき移動できます。  
    - $2$ 回チェビシェフ距離が $3$ 以下の場所に移動するとき  
チェビシェフ距離が $6$ 以下のとき移動できます。  
<b>この場合は本番でのテストケースに含まれておらず、after_contest にてテストケースが追加されました</b>。

- $3$ 回で移動できる必要十分条件  
以上以外のときは必ず $3$ 回で移動できます。

## 実装
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public static void Solve(Scanner cin)
{
	var (r1, c1, r2, c2) = cin.ReadValue<long, long, long, long>();
	var x1 = r1 + c1;
	var y1 = r1 - c1;
	var x2 = r2 + c2;
	var y2 = r2 - c2;
	if (x1 == x2 && y1 == y2)
	{
		Console.WriteLine(0);
	}
	else if (x1 == x2 || y1 == y2)
	{
		Console.WriteLine(1);
	}
	else if (Math.Abs(x1 - x2) <= 3 && Math.Abs(y1 - y2) <= 3)
	{
		Console.WriteLine(1);
	}
	else if (Math.Max(Math.Abs(x1 - x2), Math.Abs(y1 - y2)) <= 6)
	{
		Console.WriteLine(2);
	}
	else if (Math.Abs(x1 - x2) % 2 == 0)
	{
		Console.WriteLine(2);
	}
	else if (Math.Abs(x1 - x2) <= 3 || Math.Abs(y1 - y2) <= 3)
	{
		Console.WriteLine(2);
	}
	else
	{
		Console.WriteLine(3);
	}
}
```

</details>

ACコード: https://atcoder.jp/contests/abc184/submissions/18350763  
ちゃんと after_contest も AC しています。

## 感想
本番嘘解法通してしまい申し訳ありませんでした。