---
title: "yukicoder No. 1314 - N言っちゃダメゲーム (5)"
date: 2020-12-11T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロ解説","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "ネタ被りが前日に発覚して (4) が (5) になるってどんな偶然なんですか？？ 問題リンク https://yukicoder.me/problems/no/1314# 問題概要 N言っちゃダメゲームをします。つまり、先手であるあなたは から始め、お互いに の整数を加算したもの…"
aliases:
  - /entry/2020/12/11/233000
---

<!-- 解説ブログ テンプレ -->

ネタ被りが前日に発覚して (4) が (5) になるってどんな偶然なんですか？？

## 問題リンク

https://yukicoder.me/problems/no/1314#

## 問題概要

[N言っちゃダメゲーム](https://yukicoder.me/problems/no/8)をします。つまり、先手であるあなたは $0$ から始め、お互いに $\[1,\ K\]$ の整数を加算したものを言っていき、$N$ 以上の整数を言った人の負けです。  
$N$ は今回 $2$ 以上 $M$ の各整数を用います。先手の人は残っているものの中から好きなものを選び、その回の $N$ とします。一度使った数は以降使えなくなります。$2$ 回目以降のゲームは前回負けたプレイヤーが先手をします。  

お互いに各ゲームではわざと負けたりしない条件のもと勝つ回数を最大化します。あなたが勝つか負けるか、引き分けになるかを求めてください。

## 制約

$2\le M,\ K\le 10^{9}$

<!--more-->

## 考察・解法

$K$ 個先まで言える $N$ 言っちゃダメゲームにおいて、後手必勝である必要十分条件は $(N-1)\%(K+1)=0$ です。よって、先手必勝のゲーム数を $x$、後手必勝のゲーム数を $y$ とすると、$y=\lfloor (M-1)/(K+1)\rfloor ,\ x=(M-1)-y$ です。

さて、後手が勝ちのゲームは選択権は自分にあるものの相手が勝ち続けてしまうので、基本的にどちらも避けたいものです。なので、どちらも押し付けあいになります。一方先手が勝つゲームは次のゲームは負けたほうに選択権が移ります。そのため、このゲームで最適にプレイすると、お互いに先手必勝のゲームでひたすら選択権の押し付けあいをし、後手必勝のゲームだけが残るとそのとき先手の人がずっと負け続ける状態になります。

よって、先手であるあなたの勝利数は $\lceil x/2\rceil\ (x\%2=0),\ \lceil x/2\rceil +y\ (x\%2=1)$ です。

## 実装

<details><summary><u><b>実装を展開する</b></u></summary>

```cs
		public void Solve()
		{
			var (m, k) = cin.ReadValue<int, int>();
			m--;
			int lose = m / (k + 1);
			int win = m - lose;
			int first = (win + 1) / 2;
			if (win % 2 == 1) first += lose;
			if (first > m - first) Console.WriteLine("Win");
			else if (first == m - first) Console.WriteLine("Draw");
			else Console.WriteLine("Lose");
		}
```

</details>

ACコード: https://yukicoder.me/submissions/592227  

## 感想

Writer も運営も意図せずしてN言っちゃダメゲームが被ったようですね。そんなことってあるんですね……。驚きです。