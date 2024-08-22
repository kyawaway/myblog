+++
title = "Agda Installation"
template = "page.html"
date = 2024-07-08T11:00:00Z
[taxonomies]
tags = ["agda"]
[extra]
summary = "Agdaをインストールする．"
mathjax = "tex-mml"
+++

公式ドキュメントが微妙に不親切だったので，自分でまとめる．

## 1. Agda本体

### a. Homebrew経由

Homebrewがある場合は，

```shell
brew install agda
```

楽ちん．

### b. apt経由

aptでも入れられる．あんまり信頼できないバージョンが入ることもあると思うが，さっき手元で試した感じ最新バージョンが入ったしこれでも大丈夫そう．

```shell
sudo apt-get install zlib1g-dev libncurses5-dev agda
```

楽ちん．

### c. Cabal経由

Cabalから落とすのが一番安心できるが，大変だし時間もかかる．

[ドキュメント](https://agda.readthedocs.io/en/latest/getting-started/installation.html#install-agda)参照．

Haskellの環境からインストールする．Haskellの環境構築には一悶着あるが，こだわりがない場合は[GHCup](https://www.haskell.org/ghcup/)経由でCabalをinstallして，Cabalからagdaをinstallしてもらう．（2024年現在はghcupからstackもcabalも全部入れるのが主流っぽい）


```shell
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```

続いて，各種ツールのセットアップを行う：

```shell
ghcup tui
```
でrecommendedを選ぶ．すると，ghcup，HLS(HaskellのLanguage Server)，Stack(ghcのインストール，プロジェクトのビルド，ライブラリ管理ができる)，Cabal(プロジェクトのビルド，ライブラリ管理ができる)，GHC(Glasgow Haskell Compiler )がinstallできる．

完了したら，念のためインストールできているか確認する．

```shell
cabal --version
```

続いてAgdaのインストールに入る．あとは[ドキュメント](https://agda.readthedocs.io/en/latest/getting-started/installation.html#install-agda)と同じ．

```shell
apt-get install zlib1g-dev libncurses5-dev
```

```shell
cabal update
cabal install alex happy
```

```shell
cabal update
cabal install Agda
```

けっこう時間がかかる．終わったら`agda`が叩けるか試す．


```shell
agda --version
```

## 2. Agda Standard Libraries

これも入れないとろくな証明を書けない．が，[ドキュメント](https://agda.readthedocs.io/en/latest/tools/package-system.html#package-system)にはあんまり親切に書いていない．

実はagdaをインストールした時点で既に入ってはいるが，そのままでは呼び出せないのでなんとかする必要がある．

* aptで入れたら`usr/share/lock`とかのどこかに`standard-library.agda-lib`がある．

* Homebrewで入れたら`/opt/homebrew/Cellar/agda/2.6.4.3_1/lib/agda` とかに`standard-library.agda-lib`がある．

これらを認識するためのファイルがある`$AGDA_DIR`を設定する．なんでも良いが，`.agda`なるディレクトリを用意する．

```shell
cd
mkdir .agda
echo export AGDA_DIR="~/.agda" >> ~/.bash_profile
source .bash_profile
```

続いて，`.agda`の中身を作る．こんな感じの構成を用意していく．

```shell
.
├── defaults
└── libraries
```

```shell
cd .agda
touch defaults
touch libraries
```

`defaults`の中身は，

```shell
standard-library
```

`libraries`の中身は，

```shell
/PATH-TO-AGDA-STDLIB/standard-library.agda-lib
```

`PATH-TO-AGDA-STDLIB`は，`standard-library.agda-lib`があるパスを入れる．

どうしても見つからない場合は，.agda内に落としてしまうのでもよい．（正直こっちの方が保守しやすいしおすすめ）

```shell
.
├── defaults
├── lib
│   └── agda-stdlib
│        └── ...
└── libraries
```
`lib`内に `agda-stdlib`を落とす．


```shell
cd lib
git clone https://github.com/agda/agda-stdlib.git
cd ..
```

この場合の`libraries`の中身は，

```shell
~/.agda/lib/agda-stdlib/standard-library.agda-lib
```
になる．

これでok．

## 3. Editor Setup

ここからは，実際にサンプルコードを用意して動作確認する．


### VSCode

`agda-mode`を入れる．

入ったら，以下のような`hello.agda`ファイルを用意して，

```agda:hello.agda
data Greeting : Set where
  hello : Greeting

greet : Greeting
greet = hello
```
`C-c`-`C-l`を押す．(`C-n`は`Control-n`)

なんか下にAll Done.と出てきたらok．ここで，agdaのインストールに失敗している場合は，agdaが無いぞと怒られる．

続いて，以下の`data.agda`ファイルを用意し，同様に`C-c`-`C-l`を押す：

```agda:data.agda
module type where
open import Data.Nat
a : ?
a = 1
```

なんか下に
```
*All Goals*
?0 : Set
```
と出てくればok．ここで，agda-stdlibの読み込みに失敗している場合は，`Data.Set`が無いぞと怒られる．

### emacs

準備中．agda-modeを使う．

### neovim

準備中．agda-nvimかcornelisを使う．

