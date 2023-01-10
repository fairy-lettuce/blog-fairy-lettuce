---
title: "Hugo で素敵なブログ生活"
date: 2023-01-10T14:48:14+09:00
draft: false
categories: ["diary", ]
tags: ["ブログ", "日常", "技術", ]
keywords: ["Hugo", "はてなブログ", ]
---

こんにちは！おや、普段と違うブログですね！

実は、この度はてなブログから移行する先として新しいブログを開設しました。シンプルになりました。

この記事ではこのブログについての技術的な話をします。

## tl;dr

Hugo (hugo-primer テーマ) + GitHub Pages + GitHub Actions でブログを作ったよ。

2 日で出来たよ。

リポジトリは https://github.com/fairy-lettuce/fairy-lettuce.github.io で公開してるよ。

<!--more-->

## 動機

1. はてなブログはとにかく記法が厄介。  
	特に $\TeX$ まわりが非常に面倒でした。はてなブログにも導入こそできるもののどうせ KaTeX を入れるなら初めから作ってしまおうという話。
2. 自分の手元で色々動かせると楽しい。  
	あるある。
3. 手軽なWebサイト開発の勉強したい。  
	これはWebサイトと呼べるのか……？

## 採用した技術

Hugo + GitHub Pages + GitHub Actions for HUGO。テーマには [hugo-primer](https://github.com/qqpann/hugo-primer) を採用。

## やったこと

### Hugo テンプレートを用意する (大昔)

既存の Hugo テンプレートがあって、clone するだけで楽にできるというものがありました。それを用いて実装したものが[これ](https://github.com/fairy-lettuce/fairy-lettuce-blog)。見え方は[こんな感じ](https://fairy-lettuce.github.io/fairy-lettuce-blog/)。

非常に楽だったが、これではあまり意味がないと思いました。どうせカスタマイズするには結局自分で内部を覗かないといけないので折角ならここで使われている技術を勉強しようと考えました。

### Hugo を一から理解する

ということで Hugo について調べまくりました。[Hugo + GitHub Pages + GitHub Actions で独自ドメインのウェブサイトを構築する](https://zenn.dev/nikaera/articles/hugo-github-actions-for-github-pages)を専ら参考にしました。というか、これが全てだと思います。はい。これを読んだらあなたも静的なブログを作れますおめでとうございます！(嘘です、事前知識ゼロだったのでちゃんと調べないと結構苦労しました)

他にも[HugoとGitHub Pagesでブログを公開する](https://open-groove.net/other-tools/hugo-github-pages-blog/)も参考になったが、これは GitHub Actions で自動化してくれないので前者のサイトが非常に役立った。

### GitHub Pages + GitHub Actions と格闘する

ここが一番たいへんでした。GitHub Actions で自動デプロイしてほしかったので頑張って GitHub Actions について調べまくりました。

最終的に参考になったサイトは大体次のようなものです。

- [GitHub Apps経由で発行したtokenを用いて、GitHub Actionsで使う](https://zenn.dev/suzutan/articles/how-to-use-github-apps-token-in-github-actions)
- [GitHub Actions for HUGO](https://github.com/peaceiris/actions-hugo) の README.md

![デプロイに苦労する様子](/img/diary/deploy-hell.png)

デプロイに苦労していますね。

## 感想

2 日で出来てしまった。試験期間は死にました。異常心理学、お前と戦いたかった……[^1]。

ある程度納得行くものが 2 日の勉強を犠牲に出来てしまうなんて。静的サイトジェネレータ、試してみると面白いですね。

[^1]: このブログを作ったのは1月9-10日。ところで、異常心理学の試験は11日。……あれ？
