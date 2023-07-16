---
title: "yukicoder No. 1312 - Snake Eyes (Advent Calendar Contest 2020 - I) 講評"
date: 2020-12-09T23:30:00+09:00
draft: false
author: ["fairy_lettuce"]
categories: ["Writer記録","yukicoder"]
tags: ["Competitive", ]
archives: ["2020", "2020-12"]
description: "これから私が yukicoder において Writer/Tester をした問題やコンテストの講評は開催されるたびに上げようと思います。当然ですが、問題のネタバレを含みます。 私の Writer をした問題では初出題です。yukicoder 毎年恒例のアドカレコンのうち 1 問…"
aliases:
  - /entry/2020/12/09/233000
---

これから私が yukicoder において Writer/Tester をした問題やコンテストの講評は開催されるたびに上げようと思います。当然ですが、<b>問題のネタバレを含みます</b>。  

私の Writer をした問題では初出題です。yukicoder 毎年恒例のアドカレコンのうち 1 問を担当させて頂きました。色々不慣れな点もありましたが無事出題でき、良かったです。Tester のりあんさんには感謝です！

## yukicoder No.1312 Snake Eyes

[yukicoder Advent Calendar Contest 2020](https://adventar.org/calendars/5073) の 12 月 9 日公開問題です。

- <b>問題ページ</b>  
https://yukicoder.me/problems/no/1312

<!--more-->

- <b>Tester</b>  
りあん ([@rian_tkb](https://twitter.com/rian_tkb)) さん

- <b>First Accepted</b>  
hos.lyric さん・05m13s ([提出コード](https://yukicoder.me/submissions/590750))

### 制作秘話

129 という数の性質から攻めようと思い Wikipedia の 129 の性質を見てみたところ、$129=333_{(6)}$ とあったので、そこから着想して問題を作りました。  

タイトルから問題を考えることが多いので、特別なネタがあってそこから作問するのは私にしては珍しいかもしれません。

### 感想

初めての出題にしては良い問題が作れたのではないかと思います。でも考えつく問題はだいたい数学的な問題なので、もっとアルゴリズム的問題も思いつけるようになりたいです。  

今回は単発の問題を作問して出題しましたが、今度はセットまるごとを組んだり、他の方と共同でセットを組んでみたりしたいですね。また、Tester の作業にも触れておきたいです。

### 講評

いい感じに計算量を $O(\sqrt{N})$ 程度に抑えることができる、ad-hoc な数学寄りの問題です。アドカレ用の問題なので単発で出題しましたが、セットに組み込んでの出題でもいいような問題だったと思います。  

Writer 解と Tester 解が真逆のアプローチだったので最初 Tester の提出を見てびっくりしました (なんでこの問題で二分探索が出てくるんだ……って思いました) が、どちらも自然な発想の範囲内だと思います。実際に出題後の提出を見てみたところ、両方存在しました。  
しかも、Writer 解と Tester 解以外の解法も見受けられました。[Editorial](https://yukicoder.me/problems/no/1312/editorial) にリンクがありますが、[Kazun さんのユーザー解説](https://drive.google.com/file/d/1rnH3_M_BcQXCdxExG71xymA1krLqWZYN/view?usp=sharing)がそのひとつです。これすごい。

<details><summary><u><b>高速素因数分解を用いた $1\le N\le 10^{18}$ での解法について</b></u></summary>

Tester 解だと約数列挙のパートを高速素因数分解ベースに変えるだけで通るのではないかと思います。Writer 解では $O(\sqrt N)$ では (C++ 以外恐らく) 間に合わないため、これに加えて $3$ 桁のゾロ目も考慮し $O(\sqrt[3]{N})$ にする必要があります。$3$ 桁のゾロ目については、$N=a(p^2+p+1)$ という二次方程式に条件を満たす整数解があるかを調べる必要があるので、若干難易度が上がります。

</details>

また、実は、既出というレベルではないですが、Writer 解と似た解き方が想定解である問題が AtCoder にあります。

<details><summary><u><b>その問題を見る (ネタバレ防止)</b></u></summary>

[AtCoder Regular Contest 060 D - 桁和](https://atcoder.jp/contests/arc060/tasks/arc060_b)

</details>

出題後、かなりペナ祭りになっていました[^1]。平均 Try 数は約 3.2 でした。わぁ。まあ私も自分で解く際にたくさんバグらせていたのですが……。

### おまけ

Tester に指摘されて気付いたのですが、この問題の答えはなんと <b>OEIS に存在します</b> ([A059711](https://oeis.org/A059711))。まあ導出が書いてあるわけではないので別にいいんですけど……。

[^1]: `02_small_02.txt` が猛威を奮っていました。