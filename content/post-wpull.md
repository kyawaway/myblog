+++
title = "Pull-Equivalenceに関する個人的解釈"
template = "page.html"
date = 2024-10-10T13:00:00Z
[taxonomies]
tags = ["poem", "linear logic", "proof nets"]
[extra]
summary = "？c，?wをハイパーリンクとしてみれば自然？"
mathjax = "tex-mml"
+++

MELL Proof Netsに関する細かい話．

代入表現の文脈で，こんな規則が登場することがある[^1]：

<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GZgXpB5bYAAkDVD?format=jpg&name=large" width='50%'>
</div>
<div style="text-align: center">
    <img src="https://pbs.twimg.com/media/GZgXpB4aAAEjgVZ?format=jpg&name=large" width='50%'>
</div>

Promotion Boxに$?c$セルや$?w$セルを出したり入れたりしても良いという規則．この図においては同値関係として導入されているが，文献によっては両向きの書換え規則になってたり，片方だけしかなかったりする．

(余談だが，少なくとも$?w$に関しては図を見る限り明らかに対象な規則ではない．実際，$?w$のBoxに入れる方向の規則によってカット除去の合流性が失われることが知られている[^2])

多くの文献では，$?c$に関しては両向き許し[^3]，$?w$に関しては，Boxから取り出す方向のみを許している[^4]．

これらの文献に倣い，$?c$を両向き許す，$?w$を取り出す方向のみ許すとして，これらのグラフ書換えの観点からの解釈を考える．


exponentialのセルの役割は，論理式の複製，削除である．つまり，exponentialのセルはハイパーリンクとみなしても何ら問題ない．

これらのセルをハイパーリンクとみなすと，

1. $?c$は，生のハイパーリンクがBoxの中にあっても外にあっても本質的には同じであることを表す．

2. $?w$は，ハイパーリンクを終端させる役割を持つが，このようなゴミはなるべくBoxの外に出しておきたい(外にある状態を正規系とする)．

のような気持ちが見えてくる．

---

[^1]: Vaux, L.: **λ-calcul différentiel et logique classique : interactions calcula- toires.** Theses, Université de la Méditerranée - Aix-Marseille II (Jan 2007), [https://theses.hal.science/tel-00194149](https://theses.hal.science/tel-00194149)

[^2]: P. Tranquilli, **Intuitionistic differential nets and lambda-calculus,** Theoretical Computer Science, vol. 412, no. 20, pp. 1979–1997, (2011) [https://doi.org/10.1016/j.tcs.2010.12.022](https://doi.org/10.1016/j.tcs.2010.12.022).
MELLではなくDifferential Interaction Netsについての話だが．．．



[^3]: Di Cosmo, R., Guerrini, S.: **Strong Normalization of Proof Nets Modulo Structural Congruences.** In: Narendran, P., Rusinowitch, M. (eds.) Rewriting Techniques and Applications. pp. 75–89. Springer Berlin Heidelberg, Berlin, Heidelberg (1999)

[^4]: Accattoli, B.: **Linear Logic and Strong Normalization**. In: van Raams- donk, F. (ed.) 24th International Conference on Rewriting Techniques and Applications (RTA 2013). Leibniz International Proceedings in Informatics (LIPIcs), vol. 21, pp. 39–54. Schloss Dagstuhl – Leibniz-Zentrum für Infor- matik, Dagstuhl, Germany (2013).
[https://doi.org/10.4230/LIPIcs.RTA.2013.39](https://doi.org/10.4230/LIPIcs.RTA.2013.39),
iSSN: 1868-8969
