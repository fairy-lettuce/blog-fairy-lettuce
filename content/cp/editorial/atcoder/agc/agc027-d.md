---
title: "AtCoder Grand Contest 027 D - Modulo Matrix 解説"
date: 2023-07-14T23:50:00+09:00
categories: ["競プロ解説", "AtCoder"]
tags: ["Competitive", ]
draft: false
---

<!-- 解説ブログ テンプレ -->

公式解説と全然違うことをしている……。

## 問題リンク

https://atcoder.jp/contests/agc027/tasks/agc027_d

## 問題概要

$N$ が与えられます。次のような $N \times N$ 行列 $a$ を構築してください。

- $1\le a_{ij}\le 10^{15}$
- $a_{ij}$ は distinct
- 隣接する要素 $x, y$ について、$\max(x,y) \\% \min(x,y)$ の値は正かつ全て同じ

## 制約

- $2\le N \le 500$

<!--more-->

## 考察

注: 考察の道筋を書いているので長いです。簡潔な解法は下にあります。

とりあえず様々な実験をしてみます。

縦横に並べるよりも、行列を市松模様に塗り分け、片側のマスには周囲の最大公約数 (以下、LCM) $+1$ を配置していくほうが筋が良さそうです (ここまでは公式解説と同じ)。

しかし、適当にやると値が大きくなりすぎるので、うまいやり方を考えていきます。

以下、行列を市松模様に白黒で塗り分け、黒のマスには今から構築する数を、白のマスには LCM $+1$ を入れるものとします。

### 黒のマスの構築

近隣の要素の最大公約数をなるべく大きくしたいので、「隣接する要素について、片方がもう片方を割り切る」というさらに厳しい条件を課してみます。

たとえば、このような並べ方ができます。これは市松模様ではありませんが、簡単に市松模様に拡張して考えることができます。

<div>
\begin{pmatrix}
1& 2& 4& 12& 6& 3 \\
5& 10& 20& 60& 30& 10 \\
25& 50& 100& 300& 150& 50 \\
\vdots
\end{pmatrix}
</div>

このとき、より小さい数でより大きな行列を埋めたいので、適当な高度合成数の約数を並べると良いのではないかと考えられます。

しかし、$10^{15}$ 以下の最大の高度合成数には約数が $26880$ 個しかないので、その約 $10$ 倍ほど必要なこの敷き詰め方は不可能だと分かります。

一方、高度合成数 $14414400$ には約数が $504$ 個存在します。これは十分小さいので、縦横どちらかのみなら約数を並べる方針が通用しそうです。

そこで、縦横を分解して考えてみます。

### 縦と横に分解する

縦と横について、長さ $N$ の数列 $p,q$ を考えます。すべての $i,j$ について $p_i,q_j$ は互いに素であるとします。

このとき、黒いマスについて $a_{ij}=p_{i}q_{j}$ とすれば、白いマスには $\mathrm{LCM}(p_{i-1},p_i,p_{i+1},q_{i-1},q_i,q_{i+1})$ が入ります。

公式解説では斜めの $2$ 方向について分解していますが、こちらでは縦横に分解しているので前後 $3$ つずつの LCM となることに注意してください。

<div>
\begin{pmatrix}
& p_{i-1}q_{j}&  \\
p_{i}q_{j-1}& a_{ij}& p_{i}q_{j+1} \\
& p_{i+1}q_{j}& 
\end{pmatrix}
</div>

$p,q$ がどちらも素数を並べた列だと値が大きくなりすぎてしまいますが、$p$ を素数列、$q$ を先程考えた高度合成数の約数の列にすることでより小さい値を達成できます。

それでも、$506$ 番目の素数は $3617$ なので、白いマスの最大値は約 $3617^3\times 1.5\times10^7\approx 10^{18}$ になってしまい、今回の条件を満たせません。

どうにかして $p$ の素数列を圧縮してみたくなります。

### 素数列を半分にする

$p,q$ を $500$ 個ずつの数列として $a_{ij}=p_{i}q_{j}$ としていましたが、半分は白いマスなので半分無駄です。

そこで、隣り合う行の $p$ は $2$ 個ずつで同じ素数を使えばいいと考えます。つまり、構築した行列はこのようになります。

<div>
\begin{pmatrix}
p_{0}q_{0}	&				& p_{0}q_{2}		& 				& \cdots \\
			& p_{0}q_{1}	& 					& p_{0}q_{3}	& \\
p_{1}q_{0}	&				& p_{1}q_{2}		&				& \\
			& p_{1}q_{1}	& 					& p_{1}q_{3}	& \\
\vdots		& 				& 					&				& \ddots
\end{pmatrix}
</div>

このようにすることで、$p$ は隣接 $2$ つのみの素数の積が寄与となる上、$250$ 個に減らすことができます。

$256$ 番目の素数は $1619$ なので、隣接 $2$ 項の積の上界はおよそ $2.7\times 10^6$ です。よって LCM の上界はおよそ $3.8 \times 10^{13}$ です。

ただし、$q$ の隣接 $3$ 項の LCM が同じ場所が存在するため、白いマスの値が何箇所か被ってしまいます。

そのような場所は、適当に数倍した上で $+1$ をする必要があります。実際に実装してみると、余裕をもって通るので問題はありません。

## 解法

数列 $q$ を $14414400=2^6\times 3^2\times 5^2\times 7\times 11\times 13$ の約数を並べた数列とします。

ただし、$q$ の隣接する要素は片方がもう片方の素数倍になるように並べ替えます。$1,2,4\/12,6,3\/9,18,36$ のように素因数を順次増減させることで実現できます。

また、数列 $p$ を $17$ 以上の素数 $250$ 個とします。

$0\le i,j\le N-1$ について、$(i+j)\\%2=0$ ならば $a_{ij}=p_{\lfloor\frac{i}{2}\rfloor}q_j$ とします。

$(i+j)\\%2=1$ のとき、適当な正整数を $k$、隣接する数の最小公倍数を $lcm$ として、$k\times lcm+1$ とします。$k$ は $a_{ij}$ が他の場所と重複しないような最小のものを選びます。

自分の実装では、$a_{ij}$ の最大値は何も工夫をせずに $112,927,324,910,401$ に、$p, q$ の順序を最適化することで $28,477,412,964,001$ になりました。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```csharp
		int[] dx = { 0, 1, 0, -1 };
		int[] dy = { 1, 0, -1, 0 };
 
		public override void Solve()
		{
			var l = new List<long> { 1, 13 };
			l = Multiply(l, new List<long> { 1, 11 });
			l = Multiply(l, new List<long> { 1, 7 });
			l = Multiply(l, new List<long> { 1, 5, 25 });
			l = Multiply(l, new List<long> { 1, 3, 9 });
			l = Multiply(l, new List<long> { 1, 2, 4, 8, 16, 32, 64 });
			var p = CreateArray(2000, i => true);
			p[0] = false;
			p[1] = false;
			for (int i = 2; i * i < 2000; i++)
			{
				if (!p[i]) continue;
				for (int j = 2 * i; j < 2000; j += i)
				{
					p[j] = false;
				}
			}
			var primes = new List<long>();
			for (int i = 17; i < 2000; i++)
			{
				if (p[i]) primes.Add(i);
				if (primes.Count >= 250) continue;
			}
			primes = Shuffle(primes);
			var a = CreateArray(500, 500, (i, j) => (i + j) % 2 == 0 ? primes[i / 2] * l[j] : 1);
			var s = new HashSet<long>();
			for (int i = 0; i < 500; i++)
			{
				for (int j = 0; j < 500; j++)
				{
					if (a[i][j] > 1)
					{
						s.Add(a[i][j]);
					}
				}
			}
			var max = 0L;
			var maxrep = 0L;
			for (int i = 0; i < 500; i++)
			{
				for (int j = 500 - 1; j >= 0; j--)
				{
					if (a[i][j] == 1)
					{
						for (int k = 0; k < 4; k++)
						{
							var nx = i + dx[k];
							var ny = j + dy[k];
							if (nx < 0) continue;
							if (ny < 0) continue;
							if (nx >= 500) continue;
							if (ny >= 500) continue;
							a[i][j] = LeastCommonMultiple(a[i][j], a[nx][ny]);
						}
						var v = a[i][j];
						while (s.Contains(a[i][j] + 1)) a[i][j] += v;
						maxrep.Chmax(a[i][j] / v);
						a[i][j]++;
						s.Add(a[i][j]);
					}
					max.Chmax(a[i][j]);
				}
			}
			var n = sr.ReadInt();
			for (int i = 0; i < n; i++)
			{
				Console.WriteLine(a[i].Take(n).Join(" "));
			}
		}
 
		public List<long> Shuffle(List<long> a)
		{
			var ret = new long[a.Count].ToList();
			for (int i = 0; i < a.Count / 2; i++)
			{
				ret[2 * i] = a[i];
				ret[a.Count / 2 * 2 - 2 * i - 1] = a[a.Count / 2 + i];
			}
			return ret;
		}
 
		public long GreatestCommonDivisor(long a, long b)
		{
			if (a < b) return GreatestCommonDivisor(b, a);
			if (a % b == 0) return b;
			return GreatestCommonDivisor(b, a % b);
		}
 
		public long LeastCommonMultiple(long a, long b) => a / GreatestCommonDivisor(a, b) * b;
 
		public List<long> Multiply(List<long> a, List<long> b)
		{
			var ret = new List<long>();
			var rev = false;
			for (int i = 0; i < b.Count; i++)
			{
				if (rev)
				{
					for (int j = a.Count - 1; j >= 0; j--)
					{
						ret.Add(a[j] * b[i]);
					}
				}
				else
				{
					for (int j = 0; j < a.Count; j++)
					{
						ret.Add(a[j] * b[i]);
					}
				}
				rev = !rev;
			}
			return ret;
		}
```

</details>

ACコード: https://atcoder.jp/contests/agc027/submissions/43560489

## 感想

公式解説よりも変で回りくどい構築方法でしたが、使う素数の個数が $256$ 個にまで減らせるのが効いたため要素の最大値を小さく抑えることができました。

もう少し頑張れば最大値を小さくできそうですが、様々なパラメータいじりが必要そうですね。ヒューリスティックっぽい。

てかこの問題焼きなませそうでは……？
