---
title: "JOI 2015 予選 F - 財宝 (Treasures) 解説 (おまけ: C#での定数倍高速化に関する知見)"
date: 2020-11-11T17:49:13+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","JOI"]
tags: ["Competitive", ]
archives: ["2020", "2020-11"]
description: "今回の問題解説では、JOI 2015 年の予選より、財宝 (難易度 8) を解説します。 思ったより苦戦させられ、周りの C# 使いの競プロerを巻き込んで一緒に TLE と格闘したので、その経緯も含めて解説します。 C# での定数倍高速化に関する知見を纏めた記事は多くないため、…"
aliases:
  - /entry/2020/11/11/174913
---

今回の問題解説では、JOI 2015 年の予選より、財宝 (難易度 8) を解説します。  
思ったより苦戦させられ、周りの C# 使いの競プロerを巻き込んで一緒に TLE と格闘したので、その経緯も含めて解説します。  
C# での定数倍高速化に関する知見を纏めた記事は多くないため、これが C# 競プロerの皆さんに役立つ知見となると嬉しいです。

## 問題リンク
https://atcoder.jp/contests/joi2015yo/tasks/joi2015yo_f

## 問題概要
$N$ 個の財宝があり、それぞれには市場価値 $X_i$ と貴重度 $Y_i$ が定められています。これらの財宝を Anna と Bruno の2人で分配します。2 人とも取らない財宝があってもですし、2 人とも取る財宝が 0 個でもいいです。  
Anna は、Anna が取った財宝の市場価値の合計と Bruno が取った市場価値の合計の差の絶対値が $D$ 以下であれば満足します。  
一方 Bruno は財宝の貴重度の合計を大きくしたいです。Anna が満足する取り方をした上で、Bruno が取った財宝の貴重度の合計から Anna が取った財宝の貴重度の合計を引いた値の最大値を求めてください。

## 制約(満点)  

- $1\le N\le 30$  
- $0\le D,\ X_i,\ Y_i\le 10^{15}$  

<!--more-->

## 考察

$N$ 個の財宝の分配方法は全部で $3^{N}$ 通りあり、 $3^{30}\approx 2.05\times10^{14}$ よりこれを全探索するのはおよそ実行時間制限に間に合わなさそうです。しかし、<b>半分全列挙</b>では間に合いそう。半分全列挙でなんとか解を求める方法を考えてみます。

## 解法

半分全列挙なので、まず $N$ 個の財宝を前半と後半に半分ずつ分けます。財宝毎に Anna が取る、Bruno が取る、誰も取らない、の $3$ 通りがあるので、その全通りを列挙して、Anna と Bruno が取ったものの市場価値と貴重度の<b>差</b>をそれぞれ調べておきましょう。なお、差を見たいので市場価値と貴重度は Anna と Bruno それぞれの値を格納する必要はありません。  

次に、先程列挙した取り方ふたつをあわせて答えを導きます。以下、半分ずつに分けた財宝の取り方のうち、それぞれ見ていく側を「前半」、前半の取り方について条件を満たすものを選ぶ側を「後半」と呼ぶことにしましょう。  
さて、「市場価値の差の絶対値が $D$ 以下となる財宝の取り方」を調べたいですが、前半に属する取り方の市場価値の差 ($p$ とおく) について、後半に属する取り方の市場価値の差 ($q$ とおく) が満たすべき条件は区間を成します。これは、  

- $|p+q|\le D \iff -D\le p+q\le D \iff -D-p\le q\le D-p$  

というようにして分かります。よって、前半それぞれの取り方について、後半の取り方のうち市場価値が満たすべき条件は二分探索で絞り込むことが出来ます。さらに、区間の左端・右端も $p$ について単調減少であることを考慮すると、片方の取り方を事前にソートしてしゃくとり法のような探索でも区間を絞り込めます。  
市場価値が条件を満たす区間での財宝の貴重度の差の最大値を求めるには、二分探索で区間を絞り込む場合は Segment Tree、しゃくとり法で絞り込む場合は平衡二分探索木 (C++でのset) やスライド最大値のテクニックを使えばよいです。  

私の C# による実装は最後に載せます。

## 計算量の話

この問題、非常に<b>実行時間制限が厳しい</b>です。  
この解法の計算量は $\mathrm{O}(3^{\frac{N}{2}}\times \log{3^{N}})=\mathrm{O}(3^{\frac{N}{2}}\times N)$ です。制約は $N\le 30$ ですので、定数倍無しだとおよそ $3^{\frac{N}{2}}\times N = 1.4\times 10^{7}\times15 = 2.1\times 10^{8}$ ステップ実行することになります。ただでさえこれだけでもC++以外には厳しそうな見た目をしているのに、使用するデータ構造からしてものすごく定数倍が重いです。実行時間制限は 10 sec ですが、C++ でも TLE を見かける程度には実行時間制限が足りなくなります。  

そのため、先程挙げた解法の内前半と後半をまとめるパートで log のつく処理をするもの (特に二分探索+ Segment Tree ) は C++ では間に合うかもしれませんが、他の言語では間に合わない可能性がとても高いです。log 取りや定数倍高速化に励みましょう。  

私は 7 時間かかりました。

## 私の実行時間との格闘記録

私の全ての提出の一覧は[こちら](https://atcoder.jp/contests/joi2015yo/submissions?f.Task=joi2015yo_f&f.LanguageName=&f.Status=&f.User=fancy_lettuce)で確認できます。

### 私のTLEとの格闘の経緯(長いです！)

<details><summary><u><b>展開する</b></u></summary>

- [最初の提出](https://atcoder.jp/contests/joi2015yo/submissions/17936960)  
二分探索で区間を絞り込み、セグ木で最大値を得ています。TLE が出たので、計算量が壊れてないかとかセグ木が重くないかとかを疑います。この時点では手元の実行環境で 33 秒かかっています。

- [2 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937101)  
セグ木を自作のものから [ac-library-cs](https://github.com/fairy-lettuce/ac-library-cs) にあるものに変えました。そんなに実行速度は変わりませんでした。

- [3 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937219)・[4 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937226)  
$3^{i}$ を求める所が微妙に重そうだったのでメモ化しました。あとちょこちょこ重そうな所を書き換えています。3 回目ではバグを埋め込んだ (後半の組み合わせの配列が空だったため実行速度が速かった) ので 4 回目で直しています。直した後の手元の実行では 14 秒になりました。

- [5 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937260)  
`List<(long xsum, long ysum)>` を配列に変えました。`List.Add` とか重そうだと思ったので……。

- [6 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937301)  
`foreach` を `for` に書き直しました。1 秒くらい縮まりました。

- [7 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17937711)  
バグ取りです。

ここらへんで困ってしまい、Twitter の皆さんに助けを求めました。C# 使いの皆さんが親切にも頭を捻って定数倍高速化を考えていました (ありがとうございます)。

{{< tweet id="1325256065043214337" user="fairly_lettuce" >}}

死屍累々です。

- [8 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17940350)～[11 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17940548)  
ここで、二分探索+セグ木の解法をスライド最大値での解法に切り替えます。ここから 11 回目までバグ取りをしまくります。スライド最大値は初履修だったので大変でした。10 回目・11 回目 (ほぼ同じコード) では正答かつ 10.6 秒まで縮まっていて、手元の実行環境では 10 秒を切っていたため、あとひと押しというところまで来ます。

- [12 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17940648)  
アルゴリズムの本質を記載している Solve 関数に `AggressiveInlining` (インライン化を可能な限り行う C# の高速化の常套手段)を付けましたが、大して変わりませんでした。

- [13 回目の提出](https://atcoder.jp/contests/joi2015yo/submissions/17940796)  
`AggressiveInlining` を Solve 関数から Main 関数に移し (あまり関係無いかも)、また出てくる全ての `List` を配列に変えました。すると 3 秒以上縮まりようやく <span style="color: #00cc00"><b>AC</b></span> することが出来ました。

</details>

### 定数倍高速化まとめ

以上をまとめると、<b>$3^{i}$ のメモ化</b>と<b>二分探索+セグ木をスライド最大値に変える</b>ことにより log を取ったのと<b> `List` を配列に変えた</b>のが主な高速化の要因でした。最初 2 つはそれはそうという感じですが、要素数が膨大でなく 30 程度しかない List を配列に変えたのが 3 秒以上短縮に繋がったのは非常に驚きました。`List` は配列外参照の境界検知をするのが遅いらしい[^1]ため、配列アクセスの時間が非常に長かったのでしょう。この `List` には$\mathrm{O}(3^{\frac{N}{2}}\times N)$ 回アクセスしていましたし、無視できるほどではなかったのでしょうね。知識としては知っていましたがここまで劇的な高速化を出来るとは思いませんでした。

### 他の皆さんの解法紹介

<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/terry_u16/status/1325280176029069312?s=20"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


C# でも二分探索+ Priority Queue で通るようです。Priority Queue が大丈夫なら多分平衡二分探索木でも通ると思います。  
また、このコードは `List` を使っていますが `Span` の黒魔法で高速化していますね。やはり `List` のランダムアクセスは定数倍が若干重いようです。C#er は頭に入れておくと定数倍高速化が必要になった時に役立つかもしれません。


<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/terry_u16/status/1325290600195710976?s=20"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


また、半分全列挙→ソート→スライド最大値をすると、半分全列挙とスライド最大値が $\mathrm{O}(3^{\frac{N}{2}})$ に対してソート ($\mathrm{O}(3^{\frac{N}{2}}\times N)$) がボトルネックになるため一番時間を食うみたいです。それはそう……なのですが、$3^{15}$ 要素ある配列をソートさせる問題かなりヤバくないですか？  


<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/yupiteru_kun/status/1325276010506448896?s=20"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<!--more-->

<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/yupiteru_kun/status/1325278370431598592?s=20"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


ユピテルさんは定数倍高速化でセグ木を通しています。すごい……。  
`Array.Sort` に渡す `Compare` を自分で書くと高速化する、確かにと思いました。C# で比較関数が気を利かせようとして時間かかる例は[他にも](https://webbibouroku.com/Blog/Article/atcoder-abc155#outline__1_3_1)ありましたからね……。

## 最後に
後半は箇条書きで定数倍高速化の経緯をなぐり書きしたようになっていて見苦しいですが、定数倍高速化をした経験は少ないので思考回路を後から見返せるようにということであえて冗長なまま残しています (ごめんなさい！)。  
計算量も正しく AC するはずなのに実行時間制限に間に合わない場合の定数倍高速化は経験と使用言語に関する知識が重要になってくると思います。まず処理に log が不必要に乗っていないか (特に累乗などのメモ化で改善する例は多いです) の確認や枝刈り、log を落とせるなど些細でも計算量を改善できる場合は面倒でもその解法を実装してみる、それでも厳しいなら言語仕様的に遅い機能を使っていないかの確認や高速化オプションを付けられないかの確認をすることが今回の教訓です。これ以外にも定数倍高速化についてのやり方はたくさんあると思いますが、もし新たな知見を得た場合はまたブログにてまとめようと思います。  
それでは今回は解説はこれでおしまいです。次回の解説にもご期待下さい。

## 実装
非常に汚いコードです (ごめんなさい……)。なお、先述の通り区間をしゃくとり法で絞り込み、スライド最大値を用いて区間内での最大値を取得しています。  
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public static void Solve(Scanner cin)
	{
		var (n, d) = cin.ReadValue<int, long>();
		var (x, y) = cin.ReadValueArray<long, long>(n);
		var first = new (long x, long y)[(n + 1) / 2];
		var second = new (long x, long y)[n - (n + 1) / 2];
		for (int i = 0; i < n; i++)
		{
			if (i < (n + 1) / 2) first[i] = (x[i], y[i]);
			else second[i - (n + 1) / 2] = (x[i], y[i]);
		}

		var xall = x.Sum();
		var yall = y.Sum();

		var pow = new List<long>();
		long t = 1;
		for (int i = 0; i < 17; i++)
		{
			pow.Add(t);
			t *= 3;
		}

		var firstData = new (long xsum, long ysum)[pow[first.Length]];
		var secondData = new (long xsum, long ysum)[pow[second.Length]];
		for (int i = 0; i < pow[first.Length]; i++)
		{
			long xsumaf = 0;
			long ysumaf = 0;
			long xsumbf = 0;
			long ysumbf = 0;
			long xsumas = 0;
			long ysumas = 0;
			long xsumbs = 0;
			long ysumbs = 0;
			var tmp = i;
			for (int j = 0; j < first.Length; j++)
			{
				var state = tmp % 3;
				if (state == 1)
				{
					xsumaf += first[j].x;
					ysumaf += first[j].y;
					if (j < second.Length)
					{
						xsumas += second[j].x;
						ysumas += second[j].y;
					}
				}
				if (state == 2)
				{
					xsumbf += first[j].x;
					ysumbf += first[j].y;
					if (j < second.Length)
					{
						xsumbs += second[j].x;
						ysumbs += second[j].y;
					}
				}
				tmp /= 3;
			}
			firstData[i] = (xsumaf - xsumbf, ysumbf - ysumaf);
			if (i < pow[second.Length]) secondData[i] = (xsumas - xsumbs, ysumbs - ysumas);
		}
		Array.Sort(firstData);
		Array.Sort(secondData);
		var secondXsum = secondData.Select(p => p.xsum).ToArray();
		var secondYsum = secondData.Select(p => p.ysum).ToArray();

		var deq = new Deque<int>(secondXsum.Length);
		var l = secondXsum.LowerBound(-d - firstData[0].xsum) - 1;
		var r = secondXsum.UpperBound(d - firstData[0].xsum) - 1;
		long ans = long.MinValue;
		for (int i = r; i > l; i--)
		{
			while (deq.Count > 0 && secondYsum[deq.Last] < secondYsum[i]) deq.PopBack();
			deq.PushBack(i);
		}
		var ysum = firstData[0].ysum + secondYsum[deq.First];
		if (l < r && ans < ysum) ans = ysum;
		for (int i = 1; i < firstData.Length; i++)
		{
			var min = -d - firstData[i].xsum;
			var max = d - firstData[i].xsum;
			while (l >= 0 && secondXsum[l] >= min)
			{
				while (deq.Count > 0 && secondYsum[deq.Last] < secondYsum[l]) deq.PopBack();
				deq.PushBack(l);
				l--;
			}
			while (r >= 0 && max < secondXsum[r])
			{
				r--;
			}
			while (deq.Count > 0 && deq.First > r)
			{
				deq.PopFront();
			}

			if (l >= r) continue;
			ysum = firstData[i].ysum + secondYsum[deq.First];
			if (ans < ysum) ans = ysum;
		}

		Console.WriteLine(ans);
	}
```

</details>

ACコード: https://atcoder.jp/contests/joi2015yo/submissions/17940796

## 追記: 更に実行時間を短くした
ABC184-F でも典型的な半分全列挙が出題されました。その際にまた色々学び、$\log$ を完全に落とす $O(3^{\frac{N}{2}})$ 解法を書いたので、追記します。  


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://fairy-lettuce.hatenadiary.com/entry/2020/11/23/084343"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>


解法の方針は、上記記事で説明した集合をマージする bit 全探索および SWAG です。SWAG はスライド最小値と似たアルゴリズム (というよりはデータ構造？) で、最小値に限らずモノイド上の演算で扱えます。  
なお、これを実装するにあたり、一番苦労したのは実行時間制限ではなく、なんと<b>メモリ制限</b>でした。だって 64 bit の整数を $3^{15}$ 個持たせた配列 (整数だけでもおよそ $114.8\ \mathrm{MB}$) を 4 個くらい持たせてますからね……。今回は全列挙パートで新しい配列を作りマージする実装をしているので、更にメモリ制限がきつかったです。わざわざ動的に配列を確保したいところも `List` を使わずに配列を使っているのはメモリ削減のためですし、`GC.Collect()` をたくさん走らせてるのはマージで使った配列を強制的に破棄させるためです (ちなみにこれが一番よくメモリ削減に繋がりました、C# で MLE と格闘するシーンは少ないですが覚えておいて損はないかもしれません)。

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
	static class Program
	{
		public static void Solve(Scanner cin)
		{
			var (n, d) = cin.ReadValue<int, long>();
			var (x, y) = cin.ReadValueArray<long, long>(n);
 
			var fc = n / 2;
			var sc = n - fc;
 
			var first = new (long x, long y)[1];
			var second = new (long x, long y)[1];
 
			for (int i = 0; i < fc; i++)
			{
				var tmp1 = new (long x, long y)[first.Length];
				var tmp2 = new (long x, long y)[first.Length];
				for (int j = 0; j < first.Length; j++)
				{
					tmp1[j] = (first[j].x + x[i], first[j].y + y[i]);
					tmp2[j] = (first[j].x - x[i], first[j].y - y[i]);
				}
				first = Merge(first, tmp1);
				first = Merge(first, tmp2);
				GC.Collect();
			}
 
			for (int i = fc; i < n; i++)
			{
				var tmp1 = new (long x, long y)[second.Length];
				var tmp2 = new (long x, long y)[second.Length];
				for (int j = 0; j < second.Length; j++)
				{
					tmp1[j] = (second[j].x + x[i], second[j].y + y[i]);
					tmp2[j] = (second[j].x - x[i], second[j].y - y[i]);
				}
				second = Merge(second, tmp1);
				second = Merge(second, tmp2);
				GC.Collect();
			}
 
			long ans = long.MinValue;
			var swag = new SWAG<long>((x, y) => Math.Max(x, y));
			var l = 0;
			var r = 0;
			{
				var secondY = second.Select(p => p.y).ToArray();
 
				l = secondY.LowerBound(-d - first[0].x) - 1;
				r = secondY.UpperBound(d - first[0].x) - 1;
			}
			for (int i = r; i > l; i--)
			{
				swag.Push(second[i].y);
			}
 
			if (swag.Count > 0) Chmax(ref ans, swag.Prod());
			for (int i = 1; i < first.Length; i++)
			{
				while (l >= 0 && first[i].x + second[l].x >= -d)
				{
					swag.Push(second[l].y);
					l--;
				}
				while (r >= 0 && first[i].x + second[r].x > d)
				{
					if (l != r) swag.Pop();
					r--;
				}
 
				if (l >= r) continue;
				Chmax(ref ans, first[i].y + swag.Prod());
			}
 
			Console.WriteLine(ans);
		}
 
		public static T[] Merge<T>(T[] first, T[] second) where T : IComparable<T>
		{
			var ret = new T[first.Length + second.Length];
			int p = 0;
			int q = 0;
			while (p < first.Length || q < second.Length)
			{
				if (p == first.Length)
				{
					ret[p + q] = second[q];
					q++;
					continue;
				}
				if (q == second.Length)
				{
					ret[p + q] = first[p];
					p++;
					continue;
				}
				if (first[p].CompareTo(second[q]) < 0)
				{
					ret[p + q] = first[p];
					p++;
				}
				else
				{
					ret[p + q] = second[q];
					q++;
				}
			}
			return ret;
		}
	}
 
	public class SWAG<T>
	{
		Stack<(T value, T prod)> front, back;
		Func<T, T, T> monoid;
 
		public int Count { get; private set; }
 
		public SWAG(Func<T, T, T> monoid)
		{
			this.monoid = monoid;
			front = new Stack<(T value, T prod)>();
			back = new Stack<(T value, T prod)>();
		}
 
		public SWAG(IEnumerable<T> list, Func<T, T, T> monoid) : this(monoid)
		{
			foreach (var e in list)
			{
				Push(e);
			}
		}
 
		public T Prod()
		{
			if (front.Count == 0 && back.Count == 0) throw new InvalidOperationException();
			if (front.Count == 0) return back.Peek().prod;
			if (back.Count == 0) return front.Peek().prod;
			return monoid(front.Peek().prod, back.Peek().prod);
		}
 
		public void Push(T x)
		{
			Count++;
			if (back.Count == 0) back.Push((x, x));
			else back.Push((x, monoid(back.Peek().prod, x)));
		}
 
		public T Pop()
		{
			if (Count == 0) throw new InvalidOperationException();
			Count--;
			if (front.Count == 0)
			{
				while (back.Count > 0)
				{
					var e = back.Pop();
					if (front.Count == 0) front.Push((e.value, e.value));
					else front.Push((e.value, monoid(front.Peek().prod, e.value)));
				}
			}
			return front.Pop().value;
		}
	}
```

</details>

ACコード: https://atcoder.jp/contests/joi2015yo/submissions/18357018

また、square1001 さんが過去に [解説 Advent Calendar 2017](https://adventar.org/calendars/2398) で執筆された $O(3^{\frac{N}{2}})$ 解法の解説も参考としてリンクさせていただきます。square1001 さんありがとうございます！


<div class="inner-link-wrapper">
  <iframe
    class="hatenablogcard"
    style="width:100%;height:155px;max-width:680px;"
    src="https://hatenablog-parts.com/embed?url=https://drive.google.com/file/d/1mMknATB6sWxVIUSJkhpSMGIUvJOVYsYR/view"
    width="300" height="150" frameborder="0" scrolling="no">
  </iframe>
</div>

<!--more-->


[^1]: [参考](https://ikorin2.hatenablog.jp/entry/2020/04/13/131614)