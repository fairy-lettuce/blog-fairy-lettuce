---
title: "AtCoder Beginner Contest 184 F - Programming Contest 解説 (おまけ: 半分全列挙高速化について)"
date: 2020-11-23T08:43:43+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "ABC184-F Programming Contest を題材に、典型アルゴリズムである半分全列挙について詳しく解説します。実装の細かい部分まで解説します。 また、半分全列挙の高速化 (今回は主に を取る部分について) についても掘り下げていきます。 問題リンク https:/…"
aliases:
  - /entry/2020/11/23/084343
---

ABC184-F Programming Contest を題材に、典型アルゴリズムである半分全列挙について詳しく解説します。実装の細かい部分まで解説します。

また、半分全列挙の高速化 (今回は主に $\log$ を取る部分について) についても掘り下げていきます。

## 問題リンク

https://atcoder.jp/contests/abc184/tasks/abc184_f

## 問題概要

$N$ 問の問題があります。$i$ 番目の問題は解くのに $A_i$ 分かかります。

この中からいくつかの問題を選んで解くのにかかる時間の総和のうち、$T$ 分以下での最大値を求めてください。

## 制約

- $1\le N\le 40$
- $1\le T\le 10^{9}$
- $1\le A_i\le 10^{9}$

<!--more-->

## 半分全列挙の解説

### 半分全列挙とは

$N$ 個の要素それぞれを選ぶ・選ばないを自由に選べるとき、選んだ要素の総和を列挙するのには $O(2^{N})$ かかります(<b>bit 全探索</b>)。ですが、今回は $N\le 40$ という制約より、$2^{40}\approx 10^{12}$ 回程度の探索をすると時間内には間に合いそうにありません。

このような時に役立つのが<b>半分全列挙</b>[^1]です。半分全列挙とは、以下のようなアルゴリズムです。  

- 要素を半分に分け、それぞれで bit 全探索でありえる総和を列挙する。
- (それぞれの列挙した結果がソートされていない場合) 列挙したものをソートする。
- 前半と後半の結果を合わせて答えを求める。この際、全探索するのではなく<b>尺取り法</b>や<b>二分探索</b>などのアルゴリズムを使うことで高速に求められる。

これにより、計算量を $O(2^{\frac{N}{2}})$ や $O(\log{2^{N}}\times 2^{\frac{N}{2}})=O(N\times 2^{\frac{N}{2}})$ に落とすことができます。一見 bit 全探索では間に合わなさそうな問題もこれにより間に合う解が見つかることもあります。

### この問題での具体例

入力例 1 の場合、つまり $T=17,\ A=(2,\ 3,\ 5,\ 7,\ 11)$ のときを考えます。半分全列挙のやり方通りにやってみましょう。

1. 要素を半分に分けそれぞれで全探索  
数列 $A$ を前半と後半に分けます[^2]。それぞれを $A\_{1},\ A\_{2}$ とおきます。つまり、$A\_{1}=(2,\ 3),\ A_{2}=(5,\ 7,\ 11)$ です。  
次に、それぞれで全ての部分集合についての要素の総和を列挙します。これを $S\_{1},\ S_{2}$ とおきます。  
    - $S_{1}=(0,\ 2,\ 3,\ 5)$  
    - $S_{2}=(0,\ 5,\ 7,\ 11,\ 12,\ 16,\ 18,\ 23)$  

2. 前半と後半を合わせて答えを求める  
今回は二分探索で答えを求めることにします。  
$S\_1$ の要素 $p$ について、条件を満たす $S\_2$ の要素 $q$ の条件は今回の場合、$p+q\le T\iff q\le T-p$ です。これは $q$ について単調性があります。よってソートされた数列 $S_2$ に対して二分探索をすることで、条件を満たす中での最大値を求められます。  
試しに $S\_1$ の要素 $2$ について二分探索をしてみます。$A\_2$ の要素 $q$の満たすべき条件は $q\le T-p=17-2=15$ です。$A_2$ の要素のうち $15$ 以下の中で最大の値は $12$ です。よって $2$ を選んだときの要素の総和の最大値は $2+12=14$ であると分かります。  
これを $S\_1$ の要素全て ($O(2^{\frac{N}{2}})$ 個程度) について全部確認し、その中での最大値が答えです。この場合は $S\_1$ からは $5$、$S_2$ からは $12$ を選んだとき最大値 $17$ をとり、これが答えです。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, t) = sr.ReadValue<int, long>();
			var a = sr.ReadLongArray(n);
			var fn = n / 2;
			var sn = n - fn;
			var first = new long[1 << fn];
			var second = new long[1 << sn];
			for (int i = 0; i < 1 << fn; i++)
			{
				var sum = 0L;
				for (int j = 0; j < fn; j++)
				{
					if ((i & (1 << j)) > 0) sum += a[j];
				}
				first[i] = sum;
			}
			for (int i = 0; i < 1 << sn; i++)
			{
				var sum = 0L;
				for (int j = 0; j < sn; j++)
				{
					if ((i & (1 << j)) > 0) sum += a[j + fn];
				}
				second[i] = sum;
			}
			Array.Sort(first);
			Array.Sort(second);
			var ans = 0L;
			for (int i = 0; i < first.Length; i++)
			{
				var index = second.UpperBound(t - first[i]) - 1;
				if (index < 0) continue;
				ans.Chmax(first[i] + second[index]);
			}
			Console.WriteLine(ans);
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc184/submissions/22530246  

## 半分全列挙の高速化

半分全列挙は、前半で bit 全探索や、後半で二分探索をすると、計算量に $\log$ がつき $O(N\times 2^{\frac{N}{2}})$ となります。  
AtCoder はこれでも十分間に合う問題がほとんどですが、更に高速にプログラムを動作させたいとき (実行時間制限の定数倍が厳しいときなど) は余計な $\log$ を落としたいと思うこともあります。今回は、その $\log$ 落としの高速化を紹介します。

### $i$ 番目の要素を含む集合を順次追加するようにして部分集合を生成する

既に見た集合に $i$ 番目の要素を足したものを追加していくという手法です。$S=(5,\ 7,\ 11)$ のときは、  

- $S=(0)$

- $S=(0,5)$ ($5$ を足した各要素を追加)

- $S=(0,5,7,12)$ ($7$ を足した各要素を追加)

- $S=(0,5,7,12,11,16,18,23)$ ($11$ を足した各要素を追加)

となります。これだけではソートされた数列にはなりませんが、余計な $\log$ を付けずに bit 全探索できます[^3]。

### 部分集合をマージしながら生成して、常に要素がソートされているようにする

上記の工夫だけでは数列はソートされていないので、列挙後にソートする必要があり結局 $O(N\times 2^{\frac{N}{2}})$ かかってしまいます。そこで、要素を追加するときに工夫し、`set` を使わなくても常に数列がソートされている状態にします。

$S=(0,5,7,12)$ に $7$ を足した要素を追加する状況を考えます。何も考えずに追加すると $S=(0,5,7,12,11,16,18,23)$ となってしまいますが、追加される前の要素と追加された後の要素をそれぞれを小さい方から取っていき、新たな数列に格納すると $\log$ 無しに常に数列を常にソート状態に保てます！具体的には、前半と後半の数列を $A,B$、新たな数列を $C$ とすると、  
<div>$A=(0,5,7,12),\ B=(11,16,18,23),\ C=()\\A=(5,7,12),\ B=(11,16,18,23),\ C=(0)\\A=(7,12),\ B=(11,16,18,23),\ C=(0,5)\\A=(12),\ B=(11,16,18,23),\ C=(0,5,7)\\A=(12),\ B=(16,18,23),\ C=(0,5,7,11)\\\vdots$</div>  
というようにする感じです[^4]。

なお、数列ふたつをマージする際には両方ともソート済みであることが必要ですが、最初の数列は $(0)$ であるため、現れる数列は帰納的にソートされていることが示せます。

ちなみに、C++ には `inplace_merge` という便利な関数が存在します。そのため自分で実装しなくても一行で終わりです ([参考](https://cpprefjp.github.io/reference/algorithm/inplace_merge.html))。

### 尺取り法を使う

二分探索で求めていた部分は<b>尺取り法</b>[^5]で求められます。尺取り法の詳しい解説はしませんが、<b>前半の数列もソートされている</b>と仮定したとき、条件を満たす後半の数列での最大値は、前半の要素を順番に見ていくに従って index が前方に単調にずれていくことを利用した解法です。

### $\log$ の無しの実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (n, t) = sr.ReadValue<int, long>();
			var a = sr.ReadLongArray(n);
			var fn = n / 2;
			var sn = n - fn;
			var first = new long[] { 0 };
			var second = new long[] { 0 };
			for (int i = 0; i < fn; i++)
			{
				var tmp = new long[first.Length];
				for (int j = 0; j < first.Length; j++)
				{
					tmp[j] = first[j] + a[i];
				}
				first = Merge(first, tmp);
			}
			for (int i = fn; i < n; i++)
			{
				var tmp = new long[second.Length];
				for (int j = 0; j < second.Length; j++)
				{
					tmp[j] = second[j] + a[i];
				}
				second = Merge(second, tmp);
			}
 
			var ans = 0L;
			var q = second.Length - 1;
			for (int p = 0; p < first.Length; p++)
			{
				while (q >= 0 && first[p] + second[q] > t)
				{
					q--;
				}
				if (q < 0) break;
				ans.Chmax(first[p] + second[q]);
			}
			Console.WriteLine(ans);
		}
 
		public static T[] Merge<T>(T[] first, T[] second) where T : IComparable<T>
		{
			var ret = new T[first.Length + second.Length];
			var p = 0;
			var q = 0;
			var x = 0;
			while (p < first.Length || q < second.Length)
			{
				if (p == first.Length)
				{
					ret[p + q] = second[q++];
				}
				else if (q == second.Length)
				{
					ret[p + q] = first[p++];
				}
				else if (first[p].CompareTo(second[q]) < 0)
				{
					ret[p + q] = first[p++];
				}
				else
				{
					ret[p + q] = second[q++];
				}
			}
			return ret;
		}
```

</details>

ACコード: https://atcoder.jp/contests/abc184/submissions/22530165  

## おまけ

以前に解説記事にしたことのある、とある JOI の問題も半分全列挙を用いることができ、しかも $\log$ を落とすテクニックを適用できます。興味のある方は是非見てみてください。

<details><summary><u><b>展開する (ネタバレ防止)</b></u></summary>
[JOI 2015 予選 F - 財宝 (Treasures)](https://atcoder.jp/contests/joi2015yo/tasks/joi2015yo_f)


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://fairy-lettuce.hatenadiary.com/entry/2020/11/11/174913"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>

</details> 

## 参考サイト


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://algo-logic.info/split-and-list/"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>

<!--more-->

[^1]: Codeforces では Meet in the Middle と呼ばれています。
[^2]: 奇数のときは前半と後半どちらが多くなっても構いません。ここでは説明のため後半の要素が多くなっていますが、前半が多くても問題はありません。
[^3]: 特に私は通常の bit 全探索に `set` を使ってさらに $\log$ を付けてしまい、全列挙を $O(N\^2\times 2^{\frac{N}{2}})$ で行っていました。C# では通りませんでしたが C++ では通ってしまいました。
[^4]: はてなブログの TeX 環境のせいで式を揃えるのができない (`&` がエスケープ不可能) という問題があって見にくいです。申し訳ありません……。
[^5]: Codeforces では Two Pointers と呼ばれています。