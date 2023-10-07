+++
title = "ssh XForwardingが遅い"
template = "page.html"
date = 2023-10-07T11:00:00Z
[taxonomies]
tags = ["ssh","linux","x11"]
[extra]
summary = "XFowardingが遅かったからちょっと速くなるようにちょっと調べた．"
mathjax = "tex-mml"
+++


# 環境

#### local : M1 Mac

* OS : Mac OS 13.1

* CPU : M1

* Memory : 16GB

### remote : Linux

* OS : Ubuntu 22.04.3 LTS

* CPU : Intel(R) Xeon(R) Silver 4214R (いいやつ)

* Memory : 48GBくらい

# 症状

xeyesは立ち上がりは一瞬，微妙にかくつく程度．evinceが大変で，起動に1分くらいかかるし動作はかっくかく．スクロールが瞬間移動．定量的な値は取っていない．

# 試したこと

## sshの設定

sshのオプションで，データをgzipしてから転送する(-C)みたいなのがあったから，試す．いちいちsshの度に-Cをつけるのは面倒なので，クライアント(local)側の.ssh/configに以下を書き込む：

```
Host hoge
.
.
++    Compression yes
.
.
```

これで起動時間がだいたい半分くらいに，スクロールの挙動は，まあかくつくがさっきよりはマシになった．

## etc/x11 内の設定

WIP.

# まとめ

* sshのCompressionの設定でちょっとマシになった．

* WIP








