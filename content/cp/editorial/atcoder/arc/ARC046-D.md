---
title: "AtCoder Regular Contest 046 D - うさぎとマス目 解説"
date: 2021-06-01T23:09:41+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-06"]
description: "なんか全然公式解説と違うことしてました……。 問題リンク https://atcoder.jp/contests/arc046/tasks/arc046_d 問題概要 行 列からなるグリッドグラフがあります。 このグラフにおいて、 から または に移動できます。 マス からスター…"
aliases:
  - /entry/2021/06/01/230941
---

<!-- 解説ブログ テンプレ -->

なんか全然公式解説と違うことしてました……。

## 問題リンク

https://atcoder.jp/contests/arc046/tasks/arc046_d

## 問題概要

$H$ 行 $W$ 列からなるグリッドグラフがあります。

このグラフにおいて、$(i,j)$ から $((i+1) \bmod H, j)$ または $(i,(j+1) \bmod W)$ に移動できます。

マス $(0,0)$ からスタートして全てのマスを通り、$(0,0)$ に戻ってくるような経路の数を $\bmod 10^9+7$ で数え上げてください。

## 制約

$1\le H,W\le 10^6$

<!--more-->

## 考察・解法

とりあえず実験してみます。問題の図のように矢印で考えるのではなく、下のようにパスを繋ぐようにして考えてみます。


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210601230345">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/fairy_lettuce/20210601/20210601230345.jpg" alt="20210601230345">
</a>

ここで、$(H-1,j)$ から $(0,j)$ への移動を $j$ 行目の縦のワープ、$(i,W-1)$ から $(i,0)$ への移動を $i$ 列目の横のワープと呼ぶことにします。

### $H=W\ (=N)$ の場合

重要な観察として、以下のような命題が成り立ちます。

> $i=j$ のとき、$i$ 列目の横のワープと $j$ 行目の縦のワープは共存しない。

<details><summary><u><b>証明</b></u></summary>

これは $i+1$ 列目以降かつ $j+1$ 行目以降の $(N-1-i)\times(N-1-i)$ の正方形について、上・左からはパスが高々 $N-2-i$ 本しか入れないのでどのようにしてもこの正方形を全部通るようなパスはありえない、というように示せます。

</details>

> 縦横のワープする回数は合計でちょうど $N$ 回である。

<details><summary><u><b>証明</b></u></summary>

- $N$ 回未満でないことの証明  
右・下にしかいけないので、下のような敷き詰め方を考えると最低でも $N$ 個の (ワープのない) パスが必要です。  


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210601230839">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/fairy_lettuce/20210601/20210601230839.jpg" alt="20210601230839">
</a>

- $N$ 回よりも大きくないことの証明  
先程の命題から直ちに導かれます。

</details>

> 少なくとも $1$ 回以上縦・横のワープをそれぞれ含む

<details><summary><u><b>証明</b></u></summary>

全部縦・横どちらかのワープであるときを考えればありえないことは自明です。

</details>

これより、$i=0,1,\dots,N-1$ について、ワープの位置が縦か横かを $N$ か所について決め、縦横のワープどちらも含むようにすることが必要条件と分かります。

このようなワープの位置の取り方について、以下のように順番に繋げば一意にワープの位置からパスを構築出来ます。証明は略しますが、このパスが全マスを通ること (正当性) および一つしかありえないこと (一意性) は簡単に示せるでしょう。


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210601230402">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/fairy_lettuce/20210601/20210601230402.jpg" alt="20210601230402">
</a>

これで正方形のマスを全て通るパスが出来ましたが、実際にはスタート地点から動くと全てのマスを通らないような場合があります (実際、上で示したような場合はこのように半分のパスしか通りません)。


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210601230414">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/fairy_lettuce/20210601/20210601230414.jpg" alt="20210601230414">
</a>

さて、全てのパスを通る必要十分条件は次のようになります。

> $\gcd(x,y)=1$ である。ただし、$x,y$ をそれぞれ横・縦のワープの個数とする

<details><summary><u><b>証明</b></u></summary>

$g=\gcd(x,y)$ とします。

上・左辺の右上から $p$ 番目と右・下辺の右上から $p$ 番目のワープ地点どうしが結ばれています。

<figure class="figure-image figure-image-fotolife" title="説明のため、mod g の値は 1,2,3, ... , g としています">
<a href="https://f.hatena.ne.jp/fairy_lettuce/20210601230426">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/fairy_lettuce/20210601/20210601230426.jpg" alt="20210601230426">
</a><figcaption>説明のため、mod g の値は 1,2,3, ... , g としています</figcaption></figure>

ここで、$x,y$ はどちらも $g$ で割り切れるので、どの $i$ 行目の横のワープも、$j$ 列目の縦のワープも、$p \bmod g$ の値は同じです。

よって、$g\gt 1$ のときは $p\bmod g$ の値が同じようなワープ地点で $g$ 個のループを作ってしまいます。

以上より、$g=1$ が全てのパスを通る必要十分条件です。

</details>

以上より、$H=W=N$ であるときの経路の個数は、$N$ と互いに素な $i=1,2,\dots,N-1$ についての $\binom{N}{i}$ の総和です。

### $H\ne W$ の場合

下図のように、ある正方形の経路を敷き詰めたものも (スタート地点から始めると全てのマスを通るとは限りませんが) 全マスを通るパスになります。よって、一辺 $\gcd(H,W)$ の正方形のパスを $h\times w$ 個 (ただし $h=H/\gcd(H,W),\ w=W/\gcd(H,W)$ とした) 敷き詰めたパスは $H\times W$ においても valid です。

逆に、一辺 $\gcd(H,W)$ の正方形に切り分けた時にパスが作れない正方形が現れる場合はどのようにしてもパスは構築出来ません。縦横どちらかのワープ位置を決めればもう片方は一意に定まるので、$\gcd(H,W)$ の正方形繰り返し以外ではありえないことが分かります。

ここで、一辺 $\gcd(H,W)$ の正方形の横・縦のワープ位置の個数を $x,y$ とすると、全体のワープ位置の個数はそれぞれ $hx,wy$ です。

正方形のときと同様に、$\gcd(hx,wy)= 1$ がこのパスがスタート地点から始めて全マスを通る必要十分条件です。よって、答えは $\gcd(H,W)=g$ として、

> $\gcd{(hx,w(g-x) )}=1$ を満たす全ての $i=1,2,\dots, g-1$ について、$\displaystyle\binom{g}{i}$ の総和

です。これは $H=W$ のときも正しいことが確かめられます。

また特に、$g=1$ を満たすとき答えは $0$ です。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		ModInt[] factorial, finv;
 
		public void Solve()
		{
			var (h, w) = sr.ReadValue<int, int>();
			var gcd = GCD(h, w);
			Init((int)gcd);
			ModInt ans = 0;
			for (int i = 1; i < gcd; i++)
			{
				if (GCD(h / gcd * (gcd - i), w / gcd * i) > 1) continue;
				ans += Binom((int)gcd, i);
			}
			Console.WriteLine(ans);
		}
 
		public ModInt Binom(int n, int r) => factorial[n] * finv[r] * finv[n - r];
 
		public void Init(int max)
		{
			factorial = new ModInt[max + 1];
			finv = new ModInt[max + 1];
			factorial[0] = 1;
			finv[0] = 1;
			for (int i = 0; i < max; i++)
			{
				factorial[i + 1] = (i + 1) * factorial[i];
				finv[i + 1] = factorial[i + 1].Inverse();
			}
		}
 
		public static long GCD(long a, long b)
		{
			a = Math.Abs(a);
			b = Math.Abs(b);
			while (a > 0)
			{
				b %= a;
				var x = a;
				a = b;
				b = x;
			}
			return b;
		}
```

</details>

ACコード: https://atcoder.jp/contests/arc046/submissions/23113841  

## 感想

公式解説の方が圧倒的に分かりやすく、こちらのほうが (同値ではありますが) 若干考察のステップが多く、外れ方針といえるでしょう。証明も面倒です。

未証明でも普通に通ったので証明 AC って感じでした。AC してからこの解説のように厳密な証明を与えました。
