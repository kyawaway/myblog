+++
title = "SATySFiファイルの変更を検知してbuildする"
template = "page.html"
date = 2024-04-18T11:00:00Z
[taxonomies]
tags = ["latex", "satysfi", "memo"]
[extra]
summary = "保存時にbuildする"
mathjax = "tex-mml"
+++

SATySFiを，ファイル保存時にbuildしたい．

## 1. LaTeX

LaTeXの場合は，`latexmk`に`-pvc`オプションをつけると，保存時に自動で`latexmk`してくれる．

例：

```sh
latexmk -pvc -pdfdvi main.tex
```

Makefileに以下のように書いておくと便利．

```Makefile
watch:
  latexmk -pvc -pdfdvi main.tex
```

## 2. SATySFi

SATySFiの場合は，現時点では監視オプションはないため，他の方法を考える必要がある．

### 2.1. entr

`entr`を使うと，ファイルの変更を検知してコマンドを実行できる．

```sh
ls *.saty | entr -c satysfi main.saty
```

Makefileに以下のように書いておくと便利．

```Makefile
all:
	satysfi slide/slide.saty

watch:
	ls slide/slide.saty | entr make
```

(こんな書き方しても許されるんだ．)

### 2.2 エディタの機能を使う

大抵のエディタには，ファイル保存時にコマンドを実行する機能がある．


例．Vimの場合：

```vim
autocmd bufWritePost *.saty :call AutoSATySFi()<CR>

function! AutoSATySFi()
    let targetName = expand('%t')
    let result = system('satysfi ' . targetName)
    if empty(matchlist(result, '\!', 1))
        call Echomsg("succ")
    else
        let error_message = split(result,'!')[1]
        echo error_message
        split test.txt
        call Echomsg("err")
    endif
endfunction

function! Echomsg(type)
    if a:type == "succ"
        echohl StatusLine | echo "autoSATy:" | echohl ModeMsg | echon " Compilation complete"  | echohl None
    endif
    if a:type == "err"
        echohl StatusLine | echon "autoSATy:" | echohl ErrorMsg | echon " Compilation failed!" | echohl None
    endif
endfunction
```

エラー時の処理はもっと凝ったほうが良い．（quickfixに流すなど．）


### 3. まとめ

VSCodeを使ったほうがいい．

