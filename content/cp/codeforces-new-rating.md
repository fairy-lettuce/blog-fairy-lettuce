---
title: "Codeforces New Rating"
date: 2023-01-10T11:13:01+09:00
categories: ["Codeforces", "競プロその他", ]
tags: ["Codeforces", "競プロその他", "test", ]
---

初めまして。最近競プロ頑張ってるれたすです。  
日本語文献が一切無さそうな、Codeforcesに最近適用された新しいレーティング算出方法についてまとめようと思います。Codeforcesって色んな情報をまとめるのが不便なので大変ですよね……。

出典:
[Codeforces: Soon We Will Change the Rating Calculation for New Accounts](https://codeforces.com/blog/entry/77890)

## 概要
新しくCodeforcesに参加するアカウントには初回[tex:6]回のコンテストに限り表示レートに<b>マイナス補正</b>が掛かるようになります(AtCoderのレートに掛かるいわゆるリセマラ補正みたいなもの)。マイナス補正は回数を重ねる毎に少しずつ小さくなっていって[tex:6]回目の参加でゼロになります。

<!-- more -->

## 原文の翻訳
まず英語で書かれている内容をざっくりと邦訳していきます。正確ではなくある程度噛み砕いた雑な訳なので、誤訳や適切な訳があればコメントでご教示下さい。
## レート計算の具体的な方法
> - The rating of the new account will be equal to [tex:0] for display (but it will be equal to [tex:1400] when calculating rating changes).

- 新しいアカウントの表示レートは[tex:0]、内部レートは[tex:1400]になります(ここで内部レートとは、レートの計算に使うレートのことです)。

> - Suppose, after the first round, the participant gained [tex:d_1] rating points (remember that the rating was considered equal to [tex:1400] in such calculations), then in the rating display after this round [tex:500+d_1] will be displayed. Thus, after the first participation, the rating is likely to increase from [tex:0] to a value in the region of [tex:500] (plus or minus [tex:300] approximately).
> - Thus, before the second participation, the displayed rating is [tex:500+d_1], and the rating for calculating changes is [tex:1400+d_1]. Suppose a new change is [tex:d_2], then the displayed rating becomes [tex:500+d_1+350+d_2].
> - Thus, before the third participation, the displayed rating is [tex:500+d_1+350+d_2], and the rating for calculating changes is [tex:1400+d_1+d_2]. Suppose a new change is [tex:d_3], then the displayed rating becomes [tex:500+d_1+350+d_2+250+d_3].
> - And so on the first 6 rounds. Promotions of the displayed rating will be equal to [tex:500,350,250,150,100,50] (in total exactly [tex:1400]). Therefore, after participating in 6 rounds, the rating is “stabilized” and the displayed rating will be equal to the one used to recalculate the changes.

- 最初に参加したコンテストで内部レートが[tex:d_1]だけ増え、[tex:1400]から[tex:1400+d_1]に変わった場合、そのコンテスト後の表示レートは[tex:0]から[tex:500+d_1]になります。
    - 最初の参加後にレートはおよそ[tex:500\pm 300]程度になります。
- 2回目に参加するコンテストで内部レートが[tex:d_2]だけ増え、[tex:1400+d_1]から[tex:1400+d_1+d_2]に変わった場合、そのコンテスト後の表示レートは[tex:500+d_1]から[tex:500+d_1+350+d_2]になります。
- 同様に、3回目に参加するコンテストで内部レートが[tex:d_3]だけ増え、[tex:1400+d_1+d_2]から[tex:1400+d_1+d_2+d_3]に変わった場合、そのコンテスト後の表示レートは[tex:500+d_1+350+d_2]から[tex:500+d_1+350+d_2+250+d_3]になります。
- そして最初の[tex:6]回におけるレートの上昇は[tex:500,350,250,150,100,50]です。[tex:6]回の合計は[tex:1400]になるので、[tex:6]回コンテストに参加した後の表示レートはレートに計算に使っていたレート(内部レート)に等しくなります。

つまり、最初の[tex:6]回の表示レートは実際の値よりそれぞれ[tex:900,550,300,150,50,0]だけ低い値が表示されるということです。

### 計算式変更の利点
このレート変更で、以下のような利点がありそう……らしいです。
> - We are reducing the starting rating from [tex:1500] to [tex:1400], so that new accounts make a smaller contribution to the overall rating.

- 新しいアカウントの推定レートが[tex:1500]から[tex:1400]に減るので、全体のレートのインフレを若干抑えられます。

> - Currently, especially for inexperienced participants, there is an effect that their rating at the beginning of participation is constantly falling (from [tex:1500] to the value that is a true assessment of their skills). This, of course, leads to discomfort: you try, participate, solve some problems, but the rating drops.

- 今までは初心者がコンテストに参加した時、レートが[tex:1500]から真の実力のレート値になるまでずっと下がり続けてしまうので、やる気が削がれてしまいかねませんでした(が、単調増加するので少し良くなります)。

> - The previous paragraph gives additional motivation to create new accounts. If your current rating is less than [tex:1500], then there is a temptation to start from scratch with a fresh account. After the changes, such a start will require a noticeable long-term effort to climb at least your current rating. It seems that the changes should slightly reduce the motivation to create new accounts.

- 初参加でレートが下がるということは、初めて参加してレートが[tex:1500]よりも低かったら新しいアカウントを作って上振れを引くまでリセマラしようという考えになるかもしれないので、リセマラ封じになりそうです。

## 適用対象の基準
この新しいレート計算式は2020年5月24日(日本時間)に開催された、[Codeforces Round #644 \(Div. 3\)](https://codeforces.com/contest/1360)から適用されました。当初はDiv. 4の予定だったものが問題のセットがDiv. 3に適しているということで急遽Div. 3での開催に変更された上、レート計算式の変更の予告はこのDiv. 3の予告よりも後であったのにこの回から突然新計算式が適用されたというちょっと波乱の回でした。  
ちなみに私が初参加したCodeforcesのコンテストだったので、新レーティング計算式の最初の適用者になってしまったらしいです。アカウント自体は昔から作っていたので、アカウント作成時点ではなくコンテストの初参加が件のDiv. 3以降なら適用みたいです。

## 実際どういう感じで変化するの?
私のレート変化を参考までに貼っておきます。
<figure class="figure-image figure-image-fotolife" title="Rating change">[f:id:fairy_lettuce:20200624151735p:plain]<figcaption>レート変化グラフ</figcaption></figure>
<figure class="figure-image figure-image-fotolife" title="Contest results">[f:id:fairy_lettuce:20200624151848p:plain]<figcaption>6回のコンテストの結果</figcaption></figure>
こんな感じです。表示レートはそれぞれ[tex:699,1134,1351,1554,1611,1676]ですが、内部レートは[tex:1599,1684,1654,1704,1661,1676]という感じですね。私は初回から温まっているのであまり実感としては無いけれど、結構上下してるのがマイナス補正のおかげで単調増加になっているので確かにモチベは保ちやすくなりそうです。