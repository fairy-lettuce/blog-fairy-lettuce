---
title: "AtCoder Beginner Contest 188 F - +1-1x2 解説"
date: 2021-01-11T00:11:37+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","AtCoder"]
tags: ["Competitive", ]
archives: ["2021", "2021-01"]
description: "最近の ABC は調子良かったんですが、今回はありとあらゆる失敗を詰め込んだ破滅回でした。もう反省だらけです。 【追記】嘘解法であることが発覚したので、その部分を訂正して正当な解法にしました。 問題リンク https://atcoder.jp/contests/abc188/ta…"
aliases:
  - /entry/2021/01/11/001137
---

<!-- 解説ブログ テンプレ -->

最近の ABC は調子良かったんですが、今回はありとあらゆる失敗を詰め込んだ破滅回でした。もう反省だらけです。

<b>【追記】嘘解法であることが発覚したので、その部分を訂正して正当な解法にしました。</b>

## 問題リンク

https://atcoder.jp/contests/abc188/tasks/abc188_f

## 問題概要

整数 $X$ を整数 $Y$ にしたいです。以下の操作を何回でもできます。

- 整数を $1$ 増やす。
- 整数を $1$ 減らす。
- 整数を $2$ 倍する。

最小で何回操作する必要があるでしょうか。

## 制約

$1\le X,\ Y\le 10^{18}$

<!--more-->

## 考察

$Y$ の方が小さい・等しい時は答えは $X-Y$ となることは明らかです。以下、$X\lt Y$ とします。

まず、$X$ を $p$ 回 $2$ 倍したときについて考えます。大きい差はなるべく $2$ 倍で埋めればよいので、$2\^pX\le Y\lt 2\^{p+1}X$ となるような $p,\ p+1$ のみ考えればいいです。また整数に $1$ を足す・引く操作は対称的なので、$2\^pX$ から増やすのと $2\^{p+1}X$ から減らすのは同じものと考えても良いです。以下、$d=|Y-2^p|$ とします。  
この差分 $d$ を埋めるにはどうすれば良いでしょうか。$Y$ に辿り着くまでには $2$ を $p$ 回掛けているので、$2$ べきであり指数が $p$ 以下である数を足し引きすること $d$ を得るには何回足し引きすると良いか、がここでは問われています。   
足すだけなら簡単です。$d$ を $2$ 進数で見て立っている bit 数を数えるだけです。ただし、$2\^p$ を越えるぶんについては、$2^p$ で割った商の回数だけ調整する必要があります。  
では、引くことも考慮して回数を考えてみます。例えば、$d=111000\_{(2)}$ という数は $1000000\_{(2)}-1000\_{(2)}$ というように、$2$ 回足し引きするだけで求められることが分かります。他にも、$d=111001011110111_{(2)}$ という数は (具体的な足し引きは省略しますが) <s>$111$ の部分で $2$ 回、$1$ の部分で $1$ 回、$1111$ の部分で $2$ 回、$111$ の部分で $2$ 回必要です。合計して $7$ 回の足し引きで $d$ を得ることが可能です。</s>  
【<b>追記</b>】最後の $111$ 部分を $1000-1$ として $111001011111000_{(2)}$ とできます。他の部分も同様に下から見ることで対処できます。このように、$1$ が $2$ 個以上連続する場合は繰り上げをすることで $2$ 回で良いです。ただし、繰り上げは個別に計算せず、きちんと $d$ に反映させないとなりません。【<b>追記終わり</b>】  
このように、$d$ を二進数で表したとき、連続する $1$ の bit の個数が $2$ 以上なら $2$ 回、$1$ 個だけ孤立した bit なら $1$ 回として、$d$ を得るために必要な足し引きの回数が計算できます。

## 解法

$2\^pX$ が $Y$ を越えない最大の $p$ を求めておきます。$d=Y-2^pX$ とします。  
このとき、必要な操作回数は、$p, \lfloor{d/2^p}\rfloor,\ \sum \min(s,\ 2)$ の合計です。ただし、<s>$s$ は $d$ を二進数で見て、連続する $1$ が立った bit の塊それぞれについて、$1$ の個数です。</s>【<b>追記</b>】$\sum \min(s,\ 2)$ ではなく、$1$ が $1$ 個のみなら $1$ 回、$2$ 個以上連続しているならその桁に $1$ を足して繰り上げてから $1$ 回と数えた回数を足します。【<b>追記終わり</b>】  
また、$d=2\^{p+1}X-Y$ として、$2$ を掛ける回数が $1$ 多く$2^{p+1}X$ が $Y$ を越える最小のケースについても同様に操作回数を求めます。  

両者のうち小さい方が答えです。

## 実装

以下の実装は<b>修正後の (おそらく) 正当な解法</b>です。
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public void Solve()
		{
			var (x, y) = sr.ReadValue<long, long>();
			if (y <= x)
			{
				Console.WriteLine(x - y);
				return;
			}
			int p = 0;
			long t = x;
			while (t * 2 <= y)
			{
				t *= 2;
				p++;
			}
			long ans = long.MaxValue;
			{
				var diff = y - t;
				var count = Optimal(diff, p);
				ans.Chmin(count);
			}
			t *= 2;
			p++;
			{
				var diff = t - y;
				var count = Optimal(diff, p);
				ans.Chmin(count);
			}
			Console.WriteLine(ans);
		}
 
		public long Optimal(long diff, int p)
		{
			long ret = diff / (1L << p);
			diff = diff % (1L << p);
			for (int i = 0; i <= p; i++)
			{
				if ((diff & (1L << i)) > 0)
				{
					if ((diff & (1L << (i + 1))) > 0)
					{
						ret++;
						diff += (1L << i);
					}
					else ret++;
				}
			}
			return p  + ret;
		}
 
		public int PopCount(long x) => BitOperations.PopCount((uint)x);
```

</details>

ACコード: https://atcoder.jp/contests/abc188/submissions/19363968  

以下の実装は<b>嘘解法</b>です。`Optimal` メソッドの中身が異なります。
<details><summary><u><b>実装を展開する</b></u></summary>

```cs
public void Solve()
		{
			var (x, y) = sr.ReadValue<long, long>();
			if (y <= x)
			{
				Console.WriteLine(x - y);
				return;
			}
			int p = 0;
			long t = x;
			while (t * 2 <= y)
			{
				t *= 2;
				p++;
			}
			long ans = long.MaxValue;
			{
				var diff = y - t;
				var count = Optimal(diff, p);
				ans.Chmin(count);
			}
			t *= 2;
			p++;
			{
				var diff = t - y;
				var count = Optimal(diff, p);
				ans.Chmin(count);
			}
			Console.WriteLine(ans);
		}
 
		public long Optimal(long diff, int p)
		{
			long ret = diff / (1L << p);
			diff = diff % (1L << p);
			int streak = 0;
			for (int i = 0; i <= p; i++)
			{
				if ((diff & (1L << i)) > 0) streak++;
				else
				{
					ret += Min(2, streak);
					streak = 0;
				}
			}
			ret += Min(2, streak);
			return p + diff / (1L << p) + ret;
		}
 
		public int PopCount(long x) => BitOperations.PopCount((uint)x);
```

</details>

ACコード: https://atcoder.jp/contests/abc188/submissions/19356255  
after_contest 落ちました: https://atcoder.jp/contests/abc188/submissions/19363102  

## 感想・反省

問題自体がほぼ既出でした。[AGC044-A Pay to Win](https://atcoder.jp/contests/agc044/tasks/agc044_a?lang=ja) です。yukicoder にも似た問題がたくさんある気がします。そのため異様に diff が低く評価されていますが、既出でなければもう少し diff が高かったと思います (それでも黄 diff ではない気はします)。

今回の ABC の反省点を挙げていきます。

- C 問題で、エレガントな解法 (公式解説の解法 2) に固執してバグを埋め込み、結局愚直シミュレーションが間に合うことに後から気付く (愚直にシミュレートしても $N\le16$ に対して $O(N2^N)$ だということが分かっていなかった) ことで 10 分以上時間を浪費したということ。2 ペナ。
- E 問題で、$X\_i\lt Y_i$ という制約を見落としていて、SCC を持ち出す (しかもバグらせていて制約に気付いてから後戻りした)。30 分ほどの浪費。
- F 問題をあと 5 分のところで詰め切らず時間切れ。

あまりにも反省点が多かったです。今回は久しぶりに水パフォーマンスを得て冷えてしまいました。普段ならリカバリできる失敗 2 つにリカバリの失敗を重ねてしまったため破滅してしてしまった次第です。コンテスト終盤はもう心臓がバクバクでしたが通せず、終わってすぐに自分のバグを 1 つ直せば通ってしまいました。通せていればギリギリ温まっていたくらいでした。

{{< tweet id="1348265707096600577" user="fairly_lettuce" >}}

今回の失敗を忘れず、また次回似たようなミスをしないよう、また類題が出た際すぐに解けるようにしたいと思います。レートの冷えは一時的には $n$ マス戻るですが、長期的に見れば次回の成長への糧だと思って精進を続けます！

【追記】以上で解説した解法ですが、<b>Hack ケースが見つかりました</b>。

{{< tweet id="1348289865859747841" user="fairly_lettuce" >}}

{{< tweet id="1348289910977875971" user="fairly_lettuce" >}}

ということで、公式解説の DFS 解が正当な解法です。私の解法は嘘解法でした、<s>正しい解法にする方法は現状わかりません……</s>正しい解法にする方法が見つかりました。

【追記 2】正しい解法が見つかりました。


<blockquote class="twitter-tweet" >
  <p lang="ja" dir="ltr"></p>
  <a href="https://twitter.com/ygussany/status/1348292870592032776"></a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


これに合わせて、解説も訂正しました。また、Writer や運営の皆さんに after_contest も追加していただきました。ありがとうございます。
