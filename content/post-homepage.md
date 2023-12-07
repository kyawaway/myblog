+++
title = "zolaでホームページを立てた"
template = "page.html"
date = 2023-09-30T17:00:00Z
[taxonomies]
tags = ["zola","html","css","design"]
[extra]
summary = "zolaでいい感じのホームページができた．"
mathjax = "tex-mml"
+++

Rust製のSSGであるところの[zola](https://www.getzola.org/)を用いて簡単にホームページを作ってみた．最も，このブログもzola製だが．

## モノ

[これ．](https://www.ueda.info.waseda.ac.jp/~takyu/)時間の割の結構いい感じに仕上がったと思う．

repo: [https://github.com/kyawaway/mypage](https://github.com/kyawaway/mypage)

## 動機

ｵｻﾚなポートフォリオが欲しい気分だった．

## 流れ

大部分は[hermit](https://github.com/VersBinarii/hermit_zola)なるテーマを流用した．SSGのライトユーザはテーマを選んでちょろっと書き換えて終わりだと思うが，例に漏れずちょろっとやっていった．

### 導入

1. [https://www.getzola.org/documentation/getting-started/installation/](https://www.getzola.org/documentation/getting-started/installation/)を参考にインストール，動作確認する．

2. [https://github.com/VersBinarii/hermit_zola](https://github.com/VersBinarii/hermit_zola)をcloneし，中身を拝借する．本当は生zolaプロジェクトのconfigに設定を少し書き込めばよしなにやってくれるが，面倒なので丸ごと拝借した．.gitは消そう．

3. 中身を書き換えて終わり．ホームのリンクの設定に注意．

簡単すぎる．

### 変更点

配色はかなり変えた．sassファイルの，_predefined.scssとかに使いそうな色を定義しておいて，style.scssをそこそろ書き換えた．といっても，だいたいはcolorとかでファイル内検索して好きな色に書き換えるだけだし，何も難しいことはない．

あとは申し訳程度にpaddingをいじったり，あとホームにアイコンをデカデカと掲げた．それくらい．


## 結論

超簡単．みんなも軽率にやろう．



