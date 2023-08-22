---
title: "オンラインジャッジにおける C# 実行環境まとめ"
date: 2023-08-22T14:00:00+09:00
draft: false
categories: ["AtCoder", "Competitive", "競プロその他"]
tags: ["C#"]
keywords:
---

C# は仕様や機能が比較的モダン [要出典] ながらも、その歴史とともに多くの仕様を追加して増築を繰り返してきた言語です。

また、仕様が大きく異なる多様な処理系が存在したり言語仕様が昔の処理系では非常に不便だったりします。

そのため、コーディングの感覚が実行環境によって比較的左右されやすい言語です。

この記事では、競技プログラミングにおける様々なオンラインジャッジサイトの実行環境の一覧をまとめていきます。どのオンラインジャッジでどの機能が使えるかの参考にしてください。

<!-- more -->


## C# の処理系について

C# の実行環境 ( **ランタイム** ) には様々な種類があります。C++ でいう clang と g++ の関係に似ていますが、各ランタイムはかなり別物です。

各ランタイムの詳細について話すと長くなるので解説は下記ブログに譲ります。

<div style="left: 0; width: 100%; height: 190px; position: relative;"><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fsynamon.hatenablog.com%2Fentry%2F2021%2F10%2F18%2F190000" style="top: 0; left: 0; width: 100%; height: 100%; position: absolute; border: 0;" allowfullscreen scrolling="no"></iframe></div>

更に、これとは別に言語バージョンも存在します。C++11/14/17/20/23 みたいなものです。

ランタイムの種類・バージョンによってどの言語バージョンに対応しているかが異なります。

競技プログラミングをする上では以下のことを知っておけばいいと思います。大幅に端折っているので不正確な記述もありますがご容赦ください。

- .NET Framework

Microsoft による初めての .NET 実装です。

Windows 向けのランタイムなので、仮想環境上で動くオンラインジャッジでの使用は困難です。実際これが採用されているオンラインジャッジはほぼ皆無です。

(大昔の Codeforces にはあった気がします。うろ覚えなので違ったらごめん。)

しかも .NET Core / .NET に吸収統合されたため、今後これが採用されることはないでしょう。

- Xamarin

原則アプリ開発・マルチプラットフォーム対応と、使用用途が異なるため、競技プログラミングでは使用されません。

- Mono

クロスプラットフォームでオープンソースの実装です。昔は非 Windows 環境で動かすにはこれ一択だった経緯から今でも Mono を採用するオンラインジャッジは多いです。

独自の実装なので、最新のバージョン (Mono 6.x) でも少し昔の言語仕様しか使えず、C# 7.x/8 の言語仕様は一部しかサポートされていません。

- .NET Core / .NET

Microsoft の実装で、Mono 同様クロスプラットフォーム・オープンソースです。

.NET は事実上の .NET Core の後継です。.NET Core 1.x/2.x/3.x に続けて .NET 5/6/7 とバージョンが続きます。

標準的で最新の仕様・ライブラリも搭載するので、近年のオンラインジャッジはほぼこれです。この処理系を載せているオンラインジャッジはかなり信頼度が上がります。

- .NET (Native AOT)

.NET 7 以降は Native AOT によるコンパイルが可能です。

通常の C# の実行環境では JIT (Just-In-Time) といって、中間言語 (IL) で出力したプログラムを実行の都度機械語に翻訳しつつランタイム上で動かしています。

一方、AOT (Ahead-of-Time) はネイティブコード、つまり機械語に直接コンパイルするものです。

これにより、C# の実行速度はより C++ に近くなっています。およそ C++ &lt; C# AOT &lt; C# くらいです。

また、JIT で動作する言語特有の数十 ms のオーバーヘッドは完全になくなっています。

現状新 AtCoder ジャッジ以外に入っていません。

## 各オンラインジャッジの C# 実行環境一覧

日本語対応サイト・日本語圏で有名なもの ( **太字** で示している) および [clist.by/resources](https://clist.by/resources/) にあって自分が知ってるものです。 2023 年 7 月 20 日時点、筆者調べ。

日本語・英語のサイト以外は詳しくないので載せていません (ごめんなさい)。

複数ある場合はその実行環境に対応する C# の言語バージョンが新しい方のみ載せています。

### C# 対応なし

- TLX TOKI

- PKU JudgeOnline

- USACO

### Mono 4.x 系 / C# 6

古代遺跡です。

- **Topcoder** (Mono 4.x ?)

- **アルゴ式** (Mono 4.x ?)

`RuntimeInformation` を参照させてくれないので詳細なコンパイラバージョンは分からないものの、両者ともタプルがコンパイルできないのでおそらく mono 4.x 系だと思われます。

.NET Framework 4.6 の可能性も一応ありますが。

```cs
// 文字列補完 (C# 6.0)
// Compile Passed
Console.WriteLine($"{2 * 3}, {1}");

// ValueTuple (C# 7.0)
// Compile Failed
var tuple = (1, 2);
```

ところで Topcoder C# ぶっ壊れてて提出すらできません、HELP

### Mono 6.x 系

- Toph (Mono 6.0.0.313)

- CSAcademy (Mono 6.8.0.105)

- **Aizu Online Judge** (Mono 6.10.0)

- CodeChef (Mono 6.12.0.107)

- GeeksforGeeks (Mono 6.12.0.122)

- DMOJ (Mono 6.12.0.182)

- **Mojacoder** (Mono 6.12.0.182)

### .NET Core 3 / C# 8

- 旧 **AtCoder** (.NET Core 3.1.3)

### .NET 5 / C# 9

- **Library Checker (yosupo judge)** (.NET 5.0.17)

### .NET 6 / C# 10 

- Sphere Online Judge / ideone.com (.NET 6.0.0-rtm.21522.10)

- **yukicoder** (.NET 6.0.0-rc.2.21470.23)

- **HackerRank** (.NET 6.0.2)

- **Codeforces** (.NET 6.0.4)

- CodinGame (.NET 6.0.9)

- leetcode (.NET 6.0.16)

### .NET 7 / C# 11

- Kattis (.NET 7.0.302)

- 新 **AtCoder** (.NET 7.0.7)
