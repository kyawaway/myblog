+++
title = "自作beamerテンプレートkyasualの紹介"
template = "page.html"
date = 2023-07-22T17:00:00Z
[taxonomies]
tags = ["beamer","latex"]
[extra]
summary = "kyasualの紹介"
+++

[https://github.com/kyawaway/kyasual](https://github.com/kyawaway/kyasual)の紹介．

## モチベーション

世に出回ってるbeamerのテンプレートは，どれもなんかダサい，，，というかぱっと見でbeamerだなとわかる，独特の体臭がする．この原因を，

* 色合い
    - なんか青い．
    - 寒色．
* フォント
    - 細い．
    - ちっちゃい．
    - どれも大体デフォルトのフォント．

だと勝手に決めつけて，beamerっぽくないbeamerのテーマを目指した．

もう少し詳しくコンセプトを定めると，数学のゼミにも，エンジニア向けのLTにも使えるような，フォーマルさとカジュアルさを両立したデザインを目指した．用途に合わせてスライドテーマを変えるのは面倒なので，これさえ使っとけばおkみたいなものが欲しかった．

そのために，色合いはある程度カジュアルにしつつ，フォントは優等生的なもの(Noto Sans系統の，源真ゴシック)を選択した．使用感は通常のbeamerとほとんど同じだが，一部便利なアイコンやテンプレートを自前で定義した．

また．本テーマは[Mertopolis](https://github.com/matze/mtheme)や[express-beamer](https://github.com/sano-jin/express-beamer)を大いに参考にした．

## ディレクトリ構成

```
.
├── Makefile
├── latexmkrc
│
├── slide.tex
├── slide.pdf
│
├── slide
│
├── font
│   ├── GenShinGothic
│   └── TimesItalic
├── sty
│   ├── genshin.sty
│   ├── source.sty
│   └── style.sty
└── theme
    ├── beamercolorthemekyasual.sty
    ├── beamerfontthemekyasual.sty
    ├── beamerinnerthemekyasual.sty
    ├── beamerouterthemekyasual.sty
    └── beamerthemekyasual.sty
```

beamer関連の設定はbeamerディレクトリに，その他の各パッケージの管理はstyディレクトに置いている．また，フォントは使用するfontディレクトリに置いている．

## いい感じに書くために

beamerだから基本何も考えなくても壊滅的な感じにはならないけど，それでも楽にいい感じにするためにいくつか道具を定義した．

#### textblock

block環境で勝手に発生する段組みを使えば，いい感じにcontents>detailみたいな形式のまとまりが作れると考えた．が，blockは定義の記述とかに普通に使いたいから，背景色は変わらずに，勝手に発生する段組だけを拝借する，textblockなる環境を定義した．

```tex
\begin{textblock}{textblock title.}
    textblock body. textblock body.
\end{textbock}
```

で，

<div style="text-align: center">
    <img src="https://github.com/kyawaway/myblog/tree/gh-pages/images/post_kyasual/textblock.png" width='50%'>
</div>

と表示される．後述するように，itemizeとの組み合わせでよりいい感じになる．

#### itemize

ん？itemizeは元々あるだろ，と思うかもしれないが，itemizeもアイコン次第でより便利な道具になる．

##### okitem，negitem

```tex
\begin{itemize}
    \okitem{ok}
    \ngitem{ng}
\end{itemize}
```
で，

<div style="text-align: center">
    <img src="https://github.com/kyawaway/myblog/tree/gh-pages/images/post_kyasual/item.png" width='30%'>
</div>
のような表示になる．

使用例：

```tex
HogeHoge method :
\begin{columns}
    \begin{column}{0.4\textwidth}
        \begin{itemize}
            \okitem{ok point 1 !}
            \okitem{ok point 2 !}
            \okitem{ok point 3 !}
        \end{itemize}
    \end{column}
    \begin{column}{0.4\textwidth}
        \begin{itemize}
            \ngitem{neg point 1 ...}
            \ngitem{neg point 2 ...}
            \ngitem{neg point 3 ...}
        \end{itemize}
    \end{column}
\end{columns}
```

<div style="text-align: center">
    <img src="https://github.com/kyawaway/myblog/tree/gh-pages/images/post_kyasual/item-ex.png" width='70%'>
</div>

##### thusitem

```tex
\begin{itemize}
    \thusitem{thus}
\end{itemize}
```

で，

<div style="text-align: center">
    <img src="https://github.com/kyawaway/myblog/tree/gh-pages/images/post_kyasual/thus.png" width='30%'>
</div>

のような表示になる．

こんな感じで，itemize環境で勝手に発生する段組を使って，小見出しや小まとめが記述できる．textblock，itemizeを組み合わせると，

```tex
\begin{textblock}{Build}
    \begin{enumerate}
        \item{\lstinline|mv sampleslide.tex (your slide title)|}
        \item{make}
        \thusitem{(your slide title).pdf should be generated.}
    \end{enumerate}
\end{textblock}
```

<div style="text-align: center">
    <img src="https://github.com/kyawaway/myblog/tree/gh-pages/images/post_kyasual/block_item.png" width='70%'>
</div>

のように組版できる．

#### その他

細かい部分はサンプルスライドを参照されたい．

## 拡張，改造方法

#### フォント

人間だれしも好きなフォントくらいあると思うから，気軽に変えて欲しい．

デフォルトでは，

* 日本語フォント：源真ゴシック

* 英字フォント：源信ゴシック

* イタリックフォント：Times Itallic

* 数式フォント：Computer Modern（latexデフォルト）

を選択した．どれも思想に沿って優等生的だと思う．余談だが，数式フォントなんかは変えたいなあと思ってる．(issueを立ててる)なんかいい感じのフォントがあれば教えて欲しい．

xetexを使うよう指定しているので，(uplatexとかよりは)フォントまわりは楽になっている．

##### 設定方法

追加したいフォントをfontディレクトリに置いた上で，sty/genshin.styを参考に，mainfont，sansfont，monofontなどの対応するクラスにBold,Italicに設定したいフォントも含めてPath（./font/(追加したいフォント))を記述する．

記述例：

等幅フォントにGenShinGothic-Monospace-Regular.ttf(BoldにGenShinGothic-Monospace-Bold.ttf)を追加する場合，

```tex
\setmonofont[Scale = 1,
  Path = ./font/GenShinGothic/,
  BoldFont = GenShinGothic-Monospace-Bold.ttf ,
] {GenShinGothic-Monospace-Regular.ttf}
```

最後に，追加したstyファイルをstyle.sty上でインポートする．


#### 文字サイズ，行間

フォントに関する設定は，beamer/beamerfontthemekyasual に記述する．


#### タイトルページ

一番調整する機会が多い場所だと思う．実際私も発表のたびに微調整してる．tikzで描いた長方形を配置している．

設定は，beamer/beamerouterthemekyasual に記述している．

##### 文字

文字の配置を変えたい場合は，タイトルの構成要素は

```tex,linenos,linenostart=6
\begin{beamercolorbox}[wd=\paperwidth, ht=\paperheight, sep = 0pt, dp = 0pt, #1]{%
      title,
      subtitle,
      date,
      institute,
      author,
      titlegraphic
    }
```

で読み込んでいて，そのままの名前で呼び出せる．配置は，itikzで文字を配置する時のように`\node[]~`で配置する．例えば，タイトル文字の座標を変えたいときは，

```tex,linenos,linenostart=22
      \node[above right] at (1.15cm, 0.5em + 0.45\paperheight){%
      \begin{minipage}{\paperwidth-2cm}
            {\usebeamerfont{title}\usebeamercolor[fg]{title}\inserttitle\par}
      \end{minipage}
```
の22行目のat以降の数値を調整する．

##### 図形

先に述べた通り，背景の描画はtikzで長方形を書いているだけである．

例えば，タイトル文字の調整に伴い，タイトル部分の背景の黒四角の調整を行いたい場合は，

```tex,linenos,linenostart=17
    \fill [
            color = normal_text_color,
          ] (0, 0.4\paperheight) rectangle(\paperwidth, 1.2\paperheight);
```
の19行目の数値を調整する．基本的に黒色で埋めている部分は`\fill`で埋めているので，対応する文字変数があるrectangle内の`\fill`部分の座標を調整すれば良い．

#### 色

基本的に配色は全部カンで決めたので，どんどん自分好みに変えて欲しい．

テーマカラーに関する設定はbeamer/beamercolorthemekyasual に記述する．色自体はrgb形式で気合いで設定している．

主要な色は，

* `background_color`
  - 背景色．クリーム色の部分．

* `normal_text_color`
  - 文字色に関する配色．
  - ブロックのタイトルの背景色にも利用している．

* `(alerted_/exaxmple_)block_(title_/body_)color`
  - block環境関連の配色．
  - 現状，example blockの配色はコードブロックの配色にも利用してしまっている．(要改良)

変数名は後で整理します．


## 最後に

使ってね〜

