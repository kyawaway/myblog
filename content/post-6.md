+++
title = "mason-lspによるelm-lspの導入ではまった"
template = "page.html"
date = 2020-02-15T15:00:00Z
[taxonomies]
tags = ["neovim","lsp","elm"]
[extra]
summary = "tree-sitter-elm.wasmが他の場所にいた．"
mathjax = "tex-mml"
+++

## 現象

neovim 0.91

mason-lsp + mason-lsp-config でelm-lspをインストールし，.elmファイルを開いたところ，以下のエラーが出た：

```
Error executing vim.schedule lua callback: ...w/Cellar/neovim/0.9.1/share/nvim/runtime/lua/vim/lsp.lua:1309
: RPC[Error] code_name = InternalError, message = "Request initialize failed with message: ENOENT: no such
file or directory, open '../../.local/share/nvim/mason/packages/elm-language-server/node_modules/@elm-tooli
ng/elm-language-server/out/common/tree-sitter-elm.wasm'"
stack traceback:
        [C]: in function 'assert'
        ...w/Cellar/neovim/0.9.1/share/nvim/runtime/lua/vim/lsp.lua:1309: in function ''
        vim/_editor.lua: in function <vim/_editor.lua:0>
Press ENTER or type command to continue
```

tree-sitter-elm.wasmが無いよと言われている．はてさてどうしたものか．

## 原因

~/.local/share/nvim/mason/packages/elm-language-server/node_modules/@elm-tooling/elm-language-server/out/common をみてみると，確かにtree-sitter-elm.wasmは無い．

色々眺めているうちに，
~/.local/share/nvim/mason/packages/elm-language-server/node_modules/@elm-tooling/elm-language-server/out にtree-sitter-elm.wasmがあるのを発見した．これをcommon/に移動したら，正しく動いた．

なんだったんだろう．

