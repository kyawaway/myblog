+++
title = "Number(), BigInt()は空の文字列を入れると0を返す"
template = "page.html"
date = 2023-12-16T11:00:00Z
[taxonomies]
tags = ["javascript","typescript"]
[extra]
summary = "Number(), BigInt()は空の文字列を入れると0を返す"
mathjax = "tex-mml"
+++

Number(), BigInt()は空の文字列を入れると0を返す．

```javascript
Number("1")
>> 1
Number("a")
>> NaN
Number()
>> 0
Number("")
>> 0
```

```javascript
BigInt("1")
>> 1n
BigInt("a")
>> VM105:1 Uncaught SyntaxError: Cannot convert a to a BigInt
    at BigInt (<anonymous>)
    at <anonymous>:1:1
BigInt()
>> Uncaught TypeError: Cannot convert undefined to a BigInt
    at BigInt (<anonymous>)
    at <anonymous>:1:1
BigInt("")
>> 0n
```

罠．







