---
title: "Codeforces の新レーティングについてまとめてみた"
date: 2020-07-02T02:40:25+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["競プロその他","Codeforces"]
tags: ["Competitive", ]
archives: ["2020", "2020-07"]
description: "初めまして。最近競プロ頑張ってるれたすです。 日本語文献が一切無さそうな、Codeforcesに最近適用された新しいレーティング算出方法についてまとめようと思います。Codeforcesって色んな情報をまとめるのが不便なので大変ですよね……。 出典: Codeforces: So…"
aliases:
  - /entry/2020/07/02/024025
---

初めまして。最近競プロ頑張ってるれたすです。  
日本語文献が一切無さそうな、Codeforcesに最近適用された新しいレーティング算出方法についてまとめようと思います。Codeforcesって色んな情報をまとめるのが不便なので大変ですよね……。

出典:
[Codeforces: Soon We Will Change the Rating Calculation for New Accounts](https://codeforces.com/blog/entry/77890)

## 概要
新しくCodeforcesに参加するアカウントには初回$6$回のコンテストに限り表示レートに<b>マイナス補正</b>が掛かるようになります(AtCoderのレートに掛かるいわゆるリセマラ補正みたいなもの)。マイナス補正は回数を重ねる毎に少しずつ小さくなっていって$6$回目の参加でゼロになります。

<!--more-->

## 原文の翻訳
まず英語で書かれている内容をざっくりと邦訳していきます。正確ではなくある程度噛み砕いた雑な訳なので、誤訳や適切な訳があればコメントでご教示下さい。
## レート計算の具体的な方法
> - The rating of the new account will be equal to $0$ for display (but it will be equal to $1400$ when calculating rating changes).

- 新しいアカウントの表示レートは$0$、内部レートは$1400$になります(ここで内部レートとは、レートの計算に使うレートのことです)。

> - Suppose, after the first round, the participant gained $d_1$ rating points (remember that the rating was considered equal to $1400$ in such calculations), then in the rating display after this round $500+d_1$ will be displayed. Thus, after the first participation, the rating is likely to increase from $0$ to a value in the region of $500$ (plus or minus $300$ approximately).
> - Thus, before the second participation, the displayed rating is $500+d_1$, and the rating for calculating changes is $1400+d_1$. Suppose a new change is $d_2$, then the displayed rating becomes $500+d_1+350+d_2$.
> - Thus, before the third participation, the displayed rating is $500+d_1+350+d_2$, and the rating for calculating changes is $1400+d_1+d_2$. Suppose a new change is $d_3$, then the displayed rating becomes $500+d_1+350+d_2+250+d_3$.
> - And so on the first 6 rounds. Promotions of the displayed rating will be equal to $500,350,250,150,100,50$ (in total exactly $1400$). Therefore, after participating in 6 rounds, the rating is “stabilized” and the displayed rating will be equal to the one used to recalculate the changes.

- 最初に参加したコンテストで内部レートが$d_1$だけ増え、$1400$から$1400+d_1$に変わった場合、そのコンテスト後の表示レートは$0$から$500+d_1$になります。
    - 最初の参加後にレートはおよそ$500\pm 300$程度になります。
- 2回目に参加するコンテストで内部レートが$d_2$だけ増え、$1400+d_1$から$1400+d_1+d_2$に変わった場合、そのコンテスト後の表示レートは$500+d_1$から$500+d_1+350+d_2$になります。
- 同様に、3回目に参加するコンテストで内部レートが$d_3$だけ増え、$1400+d_1+d_2$から$1400+d_1+d_2+d_3$に変わった場合、そのコンテスト後の表示レートは$500+d_1+350+d_2$から$500+d_1+350+d_2+250+d_3$になります。
- そして最初の$6$回におけるレートの上昇は$500,350,250,150,100,50$です。$6$回の合計は$1400$になるので、$6$回コンテストに参加した後の表示レートはレートに計算に使っていたレート(内部レート)に等しくなります。

つまり、最初の$6$回の表示レートは実際の値よりそれぞれ$900,550,300,150,50,0$だけ低い値が表示されるということです。

### 計算式変更の利点
このレート変更で、以下のような利点がありそう……らしいです。
> - We are reducing the starting rating from $1500$ to $1400$, so that new accounts make a smaller contribution to the overall rating.

- 新しいアカウントの推定レートが$1500$から$1400$に減るので、全体のレートのインフレを若干抑えられます。

> - Currently, especially for inexperienced participants, there is an effect that their rating at the beginning of participation is constantly falling (from $1500$ to the value that is a true assessment of their skills). This, of course, leads to discomfort: you try, participate, solve some problems, but the rating drops.

- 今までは初心者がコンテストに参加した時、レートが$1500$から真の実力のレート値になるまでずっと下がり続けてしまうので、やる気が削がれてしまいかねませんでした(が、単調増加するので少し良くなります)。

> - The previous paragraph gives additional motivation to create new accounts. If your current rating is less than $1500$, then there is a temptation to start from scratch with a fresh account. After the changes, such a start will require a noticeable long-term effort to climb at least your current rating. It seems that the changes should slightly reduce the motivation to create new accounts.

- 初参加でレートが下がるということは、初めて参加してレートが$1500$よりも低かったら新しいアカウントを作って上振れを引くまでリセマラしようという考えになるかもしれないので、リセマラ封じになりそうです。

## 適用対象の基準
この新しいレート計算式は2020年5月24日(日本時間)に開催された、[Codeforces Round #644 \(Div. 3\)](https://codeforces.com/contest/1360)から適用されました。当初はDiv. 4の予定だったものが問題のセットがDiv. 3に適しているということで急遽Div. 3での開催に変更された上、レート計算式の変更の予告はこのDiv. 3の予告よりも後であったのにこの回から突然新計算式が適用されたというちょっと波乱の回でした。  
ちなみに私が初参加したCodeforcesのコンテストだったので、新レーティング計算式の最初の適用者になってしまったらしいです。アカウント自体は昔から作っていたので、アカウント作成時点ではなくコンテストの初参加が件のDiv. 3以降なら適用みたいです。

## 実際どういう感じで変化するの?
私のレート変化を参考までに貼っておきます。
<figure class="figure-image figure-image-fotolife" title="Rating change">
<a href="https://f.hatena.ne.jp/fairy_lettuce/20200624151735">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/f/fairy_lettuce/20200624/20200624151735.png" alt="20200624151735">
</a>
<figcaption>レート変化グラフ</figcaption></figure>
<figure class="figure-image figure-image-fotolife" title="Contest results">
<a href="https://f.hatena.ne.jp/fairy_lettuce/20200624151848">
  <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/f/fairy_lettuce/20200624/20200624151848.png" alt="20200624151848">
</a>
<figcaption>6回のコンテストの結果</figcaption></figure>
こんな感じです。表示レートはそれぞれ$699,1134,1351,1554,1611,1676$ですが、内部レートは$1599,1684,1654,1704,1661,1676$という感じですね。私は初回から温まっているのであまり実感としては無いけれど、結構上下してるのがマイナス補正のおかげで単調増加になっているので確かにモチベは保ちやすくなりそうです。