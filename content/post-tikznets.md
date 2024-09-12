+++
title = "TikzでProof Netsを描く"
template = "page.html"
date = 2023-12-09T11:00:00Z
[taxonomies]
tags = ["linear logic","logic","latex","tikz"]
[extra]
summary = "Proof Netsをtikzできれいに描いた"
mathjax = "tex-mml"
+++

なんでもLaTeXくんなので，Proof Netsをtikzで描く．

真剣な話，曲線を都合よく引くのはtikzの得意分野なので，複雑なグラフ構造はtikzを使うと簡単に綺麗に描ける．

## Conponents

### Cell

ただの円．一瞬．

基本的にcellを並べる作業に終始するので，tikzsetに置いておくのが良い．

```latex
\tikzset{%
  pnode/.style={%
    thick,
    circle,
    draw=black,
    fill=white,
    minimum size=#1
    },
}
```

呼び出す時は，
```latex
\node[pnode] (tensor) at (0,0) {$\otimes$};
```

のように，中心の座標を指定してやる．このとき，中に文字や式が書ける．

<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GXQGp3fawAAup8G?format=png&name=small" width='20%'>
</div>

### Wire

円から線を引く．このとき，以下のオプションを用いる:

```latex
\draw (0,0) to [out=180,in=90] (2,2);
```

と書くと，始点(0,0)から，180°の方向に出て行った線が，終点(2,2)に，90°の方向に入っていく．


cell,wireを組み合わせると，こんな感じで基本コンポーネントが描ける．

<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GXQGoWUbgAQk1i3?format=jpg&name=large" width='70%'>
</div>

```latex
\begin{tikzpicture}
    %% otimes
    \draw[very thick] (0,0) to [out=180,in=-90] (-0.7,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (-0.7,0.7)--(-0.7,0.5);
    \node[anchor=center] (arrow) at (-0.7,1) {$A$};
    \draw[very thick] (0,0) to [out=0,in=-90] (0.7,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (0.7,0.7)--(0.7,0.5);
    \node[anchor=center] (arrow) at (0.7,1) {$B$};

    \draw[very thick, -{Latex[length=2.1mm]}] (0,0)--(0,-0.8);
    \node[anchor=center] (arrow) at (0,-1) {$A \otimes B$};
    \node[pnode] (1) at (0,0) {$\otimes$};

    %% par
    \draw[very thick] (2.1,0) to [out=180,in=-90] (1.4,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (1.4,0.7)--(1.4,0.5);
    \node[anchor=center] (arrow) at (1.4,1) {$A$};

    \draw[very thick] (2.1,0) to [out=0, in=-90] (2.8,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (2.8,0.7)--(2.8,0.5);
    \node[anchor=center] (arrow) at (2.8,1) {$B$};
    \draw[very thick, -{Latex[length=2.1mm]}] (2.1,0)--(2.1,-0.8);
    \node[anchor=center] (arrow) at (2.1,-1) {$A\: \linpar \:B$};
    \node[pnode] (2) at (2.1,0) {$\linpar$};


    %% ax
    \draw[very thick, -{Latex[length=2.1mm]}] (4.2,0) to [out=180,in=90] (3.4,-0.8);
    \node[anchor=center] (arrow) at (3.5,-1) {$A^{\bot}$};
    \draw[very thick, -{Latex[length=2.1mm]}] (4.2,0) to [out=0,in=90] (5,-0.8);
    \node[anchor=center] (arrow) at (4.9,-1) {$A$};
    \node[pnode] (3) at (4.2,0) {$ax$};

    %% cut
    \draw[very thick] (6.3,0) to [out=180,in=-90] (5.6,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (5.6,0.7)--(5.6,0.5);
    \node[anchor=center] (arrow) at (5.6,1) {$A$};
    \draw[very thick] (6.3,0) to [out=0,in=-90] (7,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (7,0.7)--(7,0.5);
    \node[anchor=center] (arrow) at (7,1) {$A^{\bot}$};
    \node[pnode] (4) at (6.3,0) {$cut$};

    %% contr
    \draw[very thick] (8.4,0) to [out=180,in=-90] (7.7,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (7.7,0.7)--(7.7,0.5);
    \node[anchor=center] (arrow) at (7.7,1) {$?A$};
    \draw[very thick] (8.4,0) to [out=0,in=-90] (9.1,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (9.1,0.7)--(9.1,0.5);
    \node[anchor=center] (arrow) at (9.1,1) {$?A$};

    \draw[very thick, -{Latex[length=2.1mm]}] (8.4,0)--(8.4,-0.8);
    \node[anchor=center] (arrow) at (8.4,-1) {$?A$};
    \node[pnode] (1) at (8.4,0) {$?c$};

    %% weakn
    \draw[very thick, -{Latex[length=2.1mm]}] (10.5,0)--(10.5,-0.8);
    \node[anchor=center] (arrow) at (10.5,-1) {$?A$};
    \node[pnode] (2) at (10.5,0) {$?w$};

    %%  dist
    \node[anchor=center] (arrow) at (12.6,1) {$A$};
    \draw[very thick] (12.6,0)--(12.6,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (12.6,0.7)--(12.6,0.5);
    \draw[very thick, -{Latex[length=2.1mm]}] (12.6,0)--(12.6,-0.8);
    \node[anchor=center] (arrow) at (12.6,-1) {$?A$};
    \node[pnode] (2) at (12.6,0) {$?d$};

    %%  !
    \node[anchor=center] (arrow) at (14.7,1) {$A$};
    \draw[very thick] (14.7,0)--(14.7,0.7);
    \draw[very thick, -{Latex[length=2.1mm]}] (14.7,0.7)--(14.7,0.5);
    \draw[very thick, -{Latex[length=2.1mm]}] (14.7,0)--(14.7,-0.8);
    \node[anchor=center] (arrow) at (14.7,-1) {$!A$};
    \node[pnode] (2) at (14.7,0) {$!$};

\end{tikzpicture}
```


### Box

Exponentialが出てくる体系では，Promotion Boxを扱う．角丸の四角形を組み合わせて構成する．

<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GXQGrXdbgAAQKbg?format=png&name=900x900" width='40%'>
</div>

```latex
\begin{tikzpicture}
    \draw[thick, rounded corners=10pt, fill=red!10]
        (-3,0)--(-3,3)--(0,3)--(0,0)-- cycle;
    \draw[very thick, dashed, rounded corners=10pt, fill=white]
        (-2.6,1.5)--(-2.6,2.6)--(-0.4,2.6)--(-0.4,1.5)-- cycle;

    \node[anchor=center] (a) at (-0.8,0.9) {\fontsize{12pt}{12pt}$A$};
    \draw[very thick, -{Latex[length=2.1mm]}] (-0.8,1.5)--(a);
    \draw[very thick, -{Latex[length=2.1mm]}] (a)--(-0.8,0);
    \node[pnode] (ex) at (-0.8,0) {$!$};

    \node[anchor=center] (gamma1) at (-2.1,-1.1) {\fontsize{12}{12}$?\Gamma$};
    \draw[thick, -{Latex[length=2.1mm]}] (-1.8,1.5)--(-1.8,-0.83);
    \draw[thick, -{Latex[length=2.1mm]}] (-2.2,1.5)--(-2.2,-0.83);
    \draw[thick, -{Latex[length=2.1mm]}] (-2.4,1.5)--(-2.4,-0.83);
    \node[anchor=center] at (-2,-0.5) {$\cdots$};

    \node[anchor=center] (aex) at (-0.8,-1.1) {\fontsize{12pt}{12pt}$!A$};
    \draw[very thick, -{Latex[length=2.1mm]}] (ex)--(aex);

\end{tikzpicture}

```

## Example

部品を組み合わせて，複雑なNetを構成する．

例: $\lambda f: \mathrm{n\to n}. x: \mathrm{n}. f x$

<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GXQGtdAawAARpGG?format=jpg&name=large" width='70%'>
</div>

```latex
\begin{tikzpicture}

    \draw[ultra thick, rounded corners=10pt, fill=red!10]
        (-5.5,0)--(-5.5,3)--(0,3)--(0,0)-- cycle;

    \node[anchor=center] (a) at (-0.8,1.7) {\fontsize{14pt}{14pt}$n$};

    \node[pnodeb] (d1) at (-2.9,0.6) {\fontsize{14pt}{14pt}$?d$};
    \node[pnodeb] (w1) at (-4.5,0.6) {\fontsize{14pt}{14pt}$?w$};
    \node[anchor=center] (d1f) at (-2.9,1.7) {\fontsize{14pt}{14pt}$n^{\bot}$};
    \node[anchor=center] (f1) at (-2.9,-1.1) {\fontsize{14pt}{14pt}$?n^{\bot}$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (d1)--(f1);
    \node[anchor=center] (f2) at (-4.5,-1.1) {\fontsize{14pt}{14pt}$?(!n \otimes n^{\bot})$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (w1)--(f2);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (-1.75,2.5) to [out=0,in=90] (a);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (-1.75,2.5) to [out=0,in=90] (d1f);

    \node[pnodeb] (ax1) at (-1.75,2.5) {\fontsize{14pt}{14pt}$ax$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (d1f)--(d1);
    \node[pnodeb] (ex) at (-0.8,0) {\fontsize{14pt}{14pt}$!$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (a)--(ex);


    \node[anchor=center] (aex) at (-0.8,-1.1) {$!n$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (ex)--(aex);


    \node[anchor=center] (a2) at (-6.8,1.7) {\fontsize{14pt}{14pt}$?n^{\bot}\:\linpar\:n$};

    \node[pnodeb] (d2) at (-8.9,0.6) {\fontsize{14pt}{14pt}$?d$};
    \node[pnodeb] (w2) at (-10.5,0.6) {\fontsize{14pt}{14pt}$?w$};
    \node[anchor=center] (d2f) at (-8.9,1.7) {\fontsize{14pt}{14pt}$!n \otimes n^{\bot}$};
    \node[anchor=center] (f3) at (-8.9,-1.1) {\fontsize{14pt}{14pt}$?(!n \otimes n^{\bot})$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (d2)--(f3);
    \node[anchor=center] (f4) at (-10.5,-1.1) {\fontsize{14pt}{14pt}$?n^{\bot}$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (w2)--(f4);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (-7.75,2.5) to [out=0,in=90] (a2);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (-7.75,2.5) to [out=0,in=90] (d2f);

    \node[pnodeb] (ax2) at (-7.75,2.5) {\fontsize{14pt}{14pt}$ax$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (d2f)--(d2);

    %% ax

    \node[anchor=center] (axt1) at (0.8,-1.1) {\fontsize{14pt}{14pt}$n^{\bot}$};
    \node[anchor=center] (axt2) at (2.4,-1.1) {\fontsize{14pt}{14pt}$n$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (1.6,0) to [out=0,in=90] (axt2);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (1.6,0) to [out=180,in=90] (axt1);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (aex) to [out=-90,in=180] (0,-1.8);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (axt1) to [out=-90,in=0] (0,-1.8);

    \node[pnodeb] (t1) at (0,-1.8) {\fontsize{14pt}{14pt}$\otimes$};
    \node[pnodeb] (ax3) at (1.6,0) {\fontsize{14pt}{14pt}$ax$};
    \node[anchor=center] (tf) at (0,-2.8) {\fontsize{14pt}{14pt}$!n \otimes n^{\bot}$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (t1)--(tf);


    \draw[ultra thick, -{Latex[length=2.1mm]}] (a2) to [out=-90,in=180] (-3,-3.5);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (tf) to [out=-90,in=0] (-3,-3.5);

    \draw[ultra thick, -{Latex[length=2.1mm]}] (f1) to [out=-90,in=0] (-6,-3.2);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (f4) to [out=-90,in=180] (-6,-3.2);

    \draw[ultra thick, -{Latex[length=2.1mm]}] (f2) to [out=-90,in=0] (-7.2,-2.4);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (f3) to [out=-90,in=180] (-7.2,-2.4);


    \node[pnodeb] (cut) at (-3,-3.5) {\fontsize{14pt}{14pt}$cut$};
    \node[pnodeb] (c1) at (-7.2,-2.4) {\fontsize{14pt}{14pt}$?c$};
    \node[pnodeb] (c2) at (-6,-3.2) {\fontsize{14pt}{14pt}$?c$};

    \node[anchor=center] (c1f) at (-7.2,-3.8) {\fontsize{14pt}{14pt}$?(!n \otimes n^{\bot})$};
    \node[anchor=center] (c2f) at (-6,-4.2) {\fontsize{14pt}{14pt}$?n^{\bot}$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (c1)--(c1f);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (c2)--(c2f);


    \draw[ultra thick] (axt2)--(2.4,-4);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (2.4,-4) to [out=-90,in=0] (-3,-5);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (c2f) to [out=-90,in=180] (-3,-5);

    \node[anchor=center] (p1f) at (-3,-6) {\fontsize{14pt}{14pt}$? n^{\bot} \: \linpar \: n$};
    \node[anchor=center] (p2f) at (-4,-8) {\fontsize{14pt}{14pt}$?(!n \otimes n^{\bot}) \: \linpar \: ?n^{\bot} \:\linpar\: n $};

    \draw[ultra thick, -{Latex[length=2.1mm]}] (p1f) to [out=-90,in=0] (-4,-6.9);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (c1f) to [out=-90,in=180] (-4,-6.9);


    \node[pnodeb] (par1) at (-3,-5) {\fontsize{14pt}{14pt}$\linpar$};
    \node[pnodeb] (par2) at (-4,-6.9) {\fontsize{14pt}{14pt}$\linpar$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (par1)--(p1f);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (par2)--(p2f);


    \draw[ultra thick, rounded corners=10pt, fill=red!10]
        (3.5,-4.5)--(3.5,3)--(6.5,3)--(6.5,-4.5)-- cycle;

    \node[anchor=center] (a) at (6,1.7) {\fontsize{14pt}{14pt}$n$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (4,0.6)--(4,-0.83);
    \node[pnodeb] (d1) at (4,0.6) {\fontsize{14pt}{14pt}$?d$};
    \node[anchor=center] (d1f) at (4,1.7) {\fontsize{14pt}{14pt}$n^{\bot}$};
    \node[anchor=center] (f1) at (4,-1.1) {\fontsize{14pt}{14pt}$?n^{\bot}$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (5,2.5) to [out=0,in=90] (a);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (5,2.5) to [out=0,in=90] (d1f);

    \node[pnodeb] (ax1) at (5,2.5) {\fontsize{14pt}{14pt}$ax$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (d1f)--(d1);


    \draw[ultra thick, -{Latex[length=2.1mm]}] (a) to [out=-90,in=0] (5,-2);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (f1) to [out=-90,in=180] (5,-2);
    \node[pnodeb] (par1) at (5,-2) {\fontsize{14pt}{14pt}$\linpar$};


    \node[anchor=center] (f3) at (5,-3.5) {\fontsize{14pt}{14pt}$(?n^{\bot})\: \linpar \: n$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (par1)--(f3);
    \node[pnodeb] (ex5) at (5,-4.5) {\fontsize{14pt}{14pt}$!$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (f3)--(ex5);
    \node[anchor=center] (aex5) at (5,-5.5) {\fontsize{14pt}{14pt}$!(?n^{\bot}\: \linpar \: n)$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (ex5)--(aex5);


    \node[anchor=center] (ax51) at (7.4,-5.5) {\fontsize{14pt}{14pt}$!n \otimes n^{\bot}$};
    \node[anchor=center] (ax52) at (9.8,-5.5) {\fontsize{14pt}{14pt}$(?n^{\bot})\: \linpar \: n$};

    \draw[ultra thick, -{Latex[length=2.1mm]}] (ax51) to [out=-90,in=0] (6.2,-6.5);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (aex5) to [out=-90,in=180] (6.2,-6.5);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (8.6,-4.5) to [out=0,in=90] (ax51);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (8.6,-4.5) to [out=0,in=90] (ax52);

    \node[anchor=center] (t5f) at (6.2,-7.5) {\fontsize{14pt}{14pt}$!(?n^{\bot}\:\linpar\:n)\otimes (!n \otimes n^{\bot})$};
    \node[pnodeb] (t5) at (6.2,-6.5) {\fontsize{14pt}{14pt}$\otimes$};
    \node[pnodeb] (ax5) at (8.6,-4.5) {\fontsize{14pt}{14pt}$ax$};
    \draw[ultra thick, -{Latex[length=2.1mm]}] (t5)--(t5f);

    \draw[ultra thick, -{Latex[length=2.1mm]}] (t5f) to [out=-90,in=0] (0,-9);
    \draw[ultra thick, -{Latex[length=2.1mm]}] (p2f) to [out=-90,in=180] (0,-9);
    \node[pnodeb] (cut5) at (0,-9) {\fontsize{14pt}{14pt}$cut$};

\end{tikzpicture}

```

座標調整はまあ面倒そうに見えるかもしれないが，それ以外の，例えば曲線のケアなどの調整は全くしていない．

慣れればむしろ簡単である．
