---
title: "AtCoder Beginner Contest 190 D - Staircase Sequences 解説"
date: 2021-01-31T22:45:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-01"]
description: "ABC 反省文シリーズ。 問題リンク https://atcoder.jp/contests/abc190/tasks/abc190_d 問題概要 整数からなる公差 の等差数列のうち、総和が であるものはいくつあるでしょうか？ 制約"
aliases:
  - /entry/2021/01/31/224500
---

<!-- 解説ブログ テンプレ -->

ABC 反省文シリーズ。

## 問題リンク

https://atcoder.jp/contests/abc190/tasks/abc190_d

## 問題概要

整数からなる公差 $1$ の等差数列のうち、総和が $N$ であるものはいくつあるでしょうか？

## 制約

- $1\le N \le 10^{12}$  

<!--more-->

## 考察・解法

いくつかアプローチはあると思うので、そのうち 2 つを紹介してみることにします。  

### 解法 1

等差数列の要素のうち、中央の数および数列の項数に注目します。ただし、中央の数とは初項と末項の平均であると定義します (項数が偶数のときは整数 $+0.5$、奇数のときは整数となります)。  


<a href="https://f.hatena.ne.jp/fairy_lettuce/20210131221146">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/f/fairy_lettuce/20210131/20210131221146.png" alt="20210131221146">
</a>


さて、この等差数列の中央の数を $x$、項数を $n$ とすると、総和は $N=xn$ となります。ここで、$x$ は整数または整数を $2$ で割った数、$n$ は整数なので、$2N$ は整数となります。よって $2N$ の全ての約数について条件を満たすものを数えればよいです (約数列挙は $O(\sqrt{N})$ で出来るため間に合います)。

さて、項数 $n$ が偶数であるときは中央の数 $x$ は整数 $+0.5$ であるので、$2x$ は奇数です。項数 $n$ が奇数であるときは中央の数 $x$ は整数なので $2x$ は偶数です。以上より、$2N=pq$ となる整数 $p,\ q$ について、$p,\ q$ どちらも偶数でない場合に条件を満たす等差数列が $1$ つ構成できます。

なお、$2N=pq$ より $p,\ q$ の両方が奇数となることはありえないため、これは $2N$ の約数のうち奇数の個数を数えるのと同値です。

### 解法 2

等差数列の総和 $N$ の公式にはいくつかあります。有名なものをいくつか列挙してみましょう。  

- 初項 $a$、公差 $d=1$、項数 $n$ の等差数列の総和  
    - $\displaystyle N=\frac{1}{2} n(2a+(n-1)d)=\frac{1}{2} n(2a+n-1)$  

- 初項 $a$、項数 $n$、末項 $b$ の等差数列の総和  
    - $\displaystyle N=\frac{1}{2} n(a+b)$  

ちなみに末項については $b=a+d(n-1)$ が成り立つのでどちらの式も同値です。  

さて、これらの式を見ると、$N=\frac{1}{2} pq$ のような形の式になっていることが分かります。これより、$2N=pq$ を満たすような整数 $p,\ q$ の組を数えれば良いです。  
ここで、前者の式を見てみます。$n$ が偶数ならば $2a+n-1$ は奇数であり、$n$ が奇数ならば $2a+n-1$ は偶数となることが分かります。このように、$n,\ 2a+n-1$ の偶奇は反対のものです。後者の式でも同様に示すことができます。

よって、$2N=pq$ を満たすような整数 $p,\ q$ のうち、偶数と奇数のペアとなるようなものを数えればよいです。解法 1 同様、$2N$ の奇数の約数を数え上げるのと同値です。

## 実装

この実装では (同値ですが) 奇数の約数ではなく、偶奇の違うペアを数え上げています。本番焦りながら書いたコードなのでいつにも増して雑なコードです……。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var n = sr.ReadLong();
			var ans = 0;
			for (long i = 1; i * i <= 2 * n; i++)
			{
				if ((2 * n) % i != 0) continue;
				var c = (2 * n) / i;
				if (i % 2 == 0) { if (c % 2 == 1) ans++; }
				else { if (c % 2 == 0) ans++; }
				if (i * i != 2 * n)
				{
					if (c % 2 == 0)
					{
						if (i % 2 == 1) ans++;
					}
					else
					{
						if (i % 2 == 0) ans++;
					}
				}
			}
			Console.WriteLine(ans);
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc190/submissions/19814128  

## 感想・反省

発想さえ分かれば簡単ですが、思いの外詰まりやすい問題です。問題文が 1 行で収まるほどにシンプルな反面、詰まるとリカバリーが非常に難しいのではないでしょうか。

この問題を ABC 本番で解いたときには、いくつも失敗を重ねてしまいました。まず嘘解法を生成 (等差数列の項数は高々 $O(\sqrt{N})$ だと思いこんでいた) した後、5 分くらいでそれに気付き、想定解を実装します。しかしくだらない打ち間違い[^1]をしてしまい、これにおよそ 50 分ほど振り回されてしまいます。ABC-D 問題で詰まることはほとんどないためか頭が真っ白になってしまい、普段ならすぐ見つけられる打ち間違いを見つけるのもとても時間がかかってしまいました[^2]。  
後の問題をいくつか見て E に取り組むも時間計算量の見積もりが甘く 2 ペナを出しました。幸い F が簡単だったので一瞬で AC できたので良かったのですが、頭がこんがらがっている状況での実装は本当に辛かったです。むしろ ABCF という惨状からよくギリギリになって全完まで漕ぎ着けたと思います[^3]。

とにかく、あまりにも情けない事故でしたがレートの減少は -9 と最低限に抑えられました。次こそはこういう事故を可能な限り減らし、焦るときほど平常心を保てるようにしようと思います。今回の反省文でした。

[^1]: `if ((2 * n) % i != 0) continue;` を間違えて `if ((2 * n) % 2 != 0) continue;` としていました。あまりにもかなしい。
[^2]: その上、約数列挙した後の答えとなるようなものの条件が間違ってるの？……と考え泥沼でした。間違っていませんでした。
[^3]: この難易度の ABC で滑り込み全完は自分の中ではかなりの失敗になるので、「思うことにしています」のほうが正確かも？