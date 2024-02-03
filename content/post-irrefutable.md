+++
title = "Irrefutable Pattern"
template = "page.html"
date = 2023-01-20T11:00:00Z
[taxonomies]
tags = ["haskell", "functional programming"]
[extra]
summary = "非正格パターンマッチでは，場合によってirrefutable patternが必要になる．"
mathjax = "tex-mml"
+++

パターンマッチを非正格に評価することを考える．簡約順序によっては，パターンマッチが失敗してしまう場合がある．

```Haskell
returnOne = const 1
```

この関数に対し，パターンマッチを行うことを考える．


パターンマッチが成功する例は，

```Haskell
(\(x,y) -> returnOne y) (100,100)
```

失敗する例としては，

```Haskell
(\(x,y) -> returnOne y) undefined
```

エラーメッセージは，

```
*** Exception: Prelude.undefined
CallStack (from HasCallStack):
   error, called at libraries/base/GHC/Err.hs:79:14 in base:GHC.Err
   undefined, called at <interactive>:4:26 in interactive:Ghci4
```

`(x,y)`とundefinedがマッチできなくて怒られている．

簡約手順としては，

```
(\(x,y) -> returnOne y) undefined → \undefined -> returnOne y (error)
```

のようになっている．要するに，マッチングが早すぎる．

一方で，直感的にはこのまま簡約が進んでいっても問題はなさそうである．

```
\undefined -> returnOne y → returnOne undefined → 1
```

これを解決するために，**irrefutable pattern**なるパターンを考える．

### 1. as pattern

`variable_name@(some_pattern)`のように書く．

```
> let all@(x:xs) = [1,2,3,4]
> all
[1,2,3,4]
> x
1
> xs
[2,3,4]
```

### 2. whild carf pattern

これは単なるワイルドカード．説明不要．

### 3. lazy pattern

`~`で表記する．

値`v`の，`~pat`に対する照合は，`pat`がなんであろうと成功する．

これを用いて最初の例を記述する．

```Haskell
\~(x,y) -> returnOne y
```

みたいに書くと，最初の想定通りに動く．







