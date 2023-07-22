+++
title = "(過去記事転載)DjangoにおけるCSRF対策についてメモ"
template = "page.html"
date = 2020-10-20T16:00:00Z
tags = ["backend","python","django","security"]
[extra]
summary = "Djangoの{% csrf_token %}を追った．"
mermaid = "mermaid"
+++

<span style="color: red; ">※2020年時点の内容です！</span>

チーム開発でDjangoを使うことになって、自分は使うのが初めてなので急いで勉強中です。

Djangoでは、html上に`{% csrf_token %}`と記述すると、CSRF対策になるとされています。この記述がどんなふうにCSRF対策になっているかをメモしていきます。

## CSRFのフロー

まずはCSRFのフローを見ていきます。
ここでは、例としてTwitterのような投稿型のウェブサイトを想定して(Twitterとかはだいぶ違うけど、イメージとして)、CSRFによる乗っ取り攻撃を例にしてみます。


#### ①正常なフロー

まずはサービスの正常な機能例です。ユーザーがサービスにログインして投稿を行うフローを示します。

{% mermaid() %}
sequenceDiagram
ユーザー->>ウェブサーバ(正規):①ログインする/id,passwordを渡す
ウェブサーバ(正規)->>ウェブサーバ(正規):②id,passwordを認証する
ウェブサーバ(正規)->>ウェブサーバ(正規):③セッションを作成
ウェブサーバ(正規)->>ユーザー:④セッションを渡す、ログイン後ページを表示
ユーザー->>ウェブサーバ(正規):⑤投稿を作成、投稿データとセッションを渡す
ウェブサーバ(正規)->>ウェブサーバ(正規):⑥セッションidの一致確認後、投稿をアップロード
{% end %}
<div style="text-align: center;">図1:サービスの正常な機能例</div>

④ではセッションidはCookieによって渡されます。⑥ではCookieによって渡されたセッションidとサーバー側のセッションidを比較します。これによって、サーバーからセッションidを受け取っていない外部からの投稿を受け付けません。


#### ②攻撃例のフロー

CSRFでの攻撃例は以下の通りです。

{% mermaid() %}
sequenceDiagram
ユーザー->>ウェブサーバ(正規):①ログインする/id,passwordを渡す
ウェブサーバ(正規)->>ウェブサーバ(正規):②id,passwordを認証する
ウェブサーバ(正規)->>ウェブサーバ(正規):③セッションを作成
ウェブサーバ(正規)->>ユーザー:④セッションを渡す、ログイン後ページを表示
攻撃者-->ユーザー:⑤攻撃用のURLを踏ませる(勝手に踏んでもらう)
ウェブサーバ(攻撃用)->>ユーザー:⑥攻撃用のHTMLを返す
ユーザー->>ユーザー:⑦攻撃用HTMLに含まれている攻撃用スクリプトによって投稿が作成されている


ユーザー->>ウェブサーバ(正規):⑧(攻撃用スクリプトによって)投稿データを渡す


ウェブサーバ(正規)->>ウェブサーバ(正規):⑨セッションidの一致確認後、投稿をアップロード
{% end %}

<div style="text-align: center;">図2:CSRF攻撃の例</div>

攻撃用のスクリプトを含んだHTMLの内容については今回は詳しくは触れませんが、外部で作成した投稿をユーザー側に残っているセッションを利用してアップロードさせているという感じです。セッションidはCokkieによって保持されているので、ユーザーの手元から投稿されるような形ならば、スクリプトによって自動で投稿されてもその投稿は正規のセッションidを含みます。



## 一般的なCSRF対策

上の図より、CSRFの原因は、**セッションによる本人確認が不十分である**ことがあげられます。

このことから、**セッション以外で、本人確認を行う何か別の隠しステータスをリアルタイムで用意する必要がある**、という発想が出てきます。

これを上の図2に対し実装してみます。

{% mermaid() %}
sequenceDiagram
ユーザー->>ウェブサーバ(正規):①ログインする/id,passwordを渡す
ウェブサーバ(正規)->>ウェブサーバ(正規):②id,passwordを認証する
ウェブサーバ(正規)->>ウェブサーバ(正規):③セッションを作成
ウェブサーバ(正規)->>ウェブサーバ(正規):④ランダムな文字列を作成(以後TOKENと呼ぶ)
ウェブサーバ(正規)->>ユーザー:⑤セッションid、TOKENを渡す、ログイン後ページを表示
攻撃者-->ユーザー:⑥攻撃用のURLを踏ませる(勝手に踏んでもらう)
ウェブサーバ(攻撃用)->>ユーザー:⑦攻撃用のHTMLを返す
ユーザー->>ユーザー:⑧攻撃用HTMLに含まれている攻撃用スクリプトによって投稿が作成されている


ユーザー->>ウェブサーバ(正規):⑨(攻撃用スクリプトによって)投稿データを渡す


ウェブサーバ(正規)->>ウェブサーバ(正規):⑩セッションidは一致するが、TOKENが取得できないのでアプロードできない
{% end %}


<div style="text-align: center;">図3:CSRF攻撃の例</div>  

このTOKENは、セッションidとは別に[hidden](http://www.htmq.com/html/input_hidden.shtml)
で送ります。
そのためスクリプトによって自動で投稿されてもTOKENの値は含まれずに、正規のサーバーによってはじくことができます。

## Djangoの`{% csrf_token %}`

本題です。
書いてある通り、CSRF_TOKENというトークンを発行します。

HTMLにどう展開されているのかを見てみます。例えば、テンプレートに
```html
<form action="auth" method="post">
    {% csrf_token %}
```
と記述すると、HTMLでは、
```html
<input type='hidden' name='csrfmiddlewaretoken' value='値' />
```

のように展開されます。

このhiddenを指定されている`csrfmiddlewaretoken`というフィールドが、先に挙げた**セッション以外の、本人確認を行う何か別の隠しステータス**、上でいうTOKENにあたります。

もう少し詳しく、[django.middleware.csrf.pyのソース](https://github.com/django/django/blob/master/django/middleware/csrf.py)
を見ていきます。

##### ① _get_new_csrf_token()

django/middleware/csrf.py
```python,linenos,linenostart=70
def _get_new_csrf_token():
    return _mask_cipher_secret(_get_new_csrf_string())
```
csrfトークンを生成しているっぽい部分です。`_mask_cipher_secret`なる関数がcsrfトークンの正体っぽいので探してみます。

##### ② _mask_cipher_secret(secret)

django/middleware/csrf.py
```python,linenos,linenostart=57
def _mask_cipher_secret(secret):
    """
    Given a secret (assumed to be a string of CSRF_ALLOWED_CHARS), generate a
    token by adding a mask and applying it to the secret.
    """
    mask = _get_new_csrf_string()
    chars = CSRF_ALLOWED_CHARS
    pairs = zip((chars.index(x) for x in secret), (chars.index(x) for x in mask))
    cipher = ''.join(chars[(x + y) % len(chars)] for x, y in pairs)
    return mask + cipher
```
なんかいろいろ書いてあります。まず、

django/middleware/csrf.py
```python,linenos,linenostart=62
    mask = _get_new_csrf_string()
```
という新たな関数が登場しています。これは後ほど見ていきます。

その下で出てくる`CSRF_ALLOWED_CHARS`に関しては、

django/middleware/csrf.py
```python,linenos,linenostart=32
CSRF_ALLOWED_CHARS = string.ascii_letters + string.digits
```
と定義されています。
`string.ascii_letters` は、
文字列定数"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"ですね。
`string.digits` は、
文字列定数"0123456789"です。
要するに、`CSRF_ALLOWED_CHARS`は、アルファベット全てと数字0~9を全部含んだ文字列定数だということです。一体これだけの文字を集めて何をするのでしょう...❓

django/middleware/csrf.py
```python,linenos,linenostart=64
    pairs = zip((chars.index(x) for x in secret), (chars.index(x) for x in mask))
```
これをもう少し細か見てみます。
```python
chars.index(x) for x in secret
```
の部分では、`secret`の要素一つ一つの、`chars`におけるインデックス(何番目か)を頭から順番に表しています(`secret`はこの関数、 `_mask_cipher_secret`(`secret`)の引数です)、その直後でも`secret`が`mask`になっただけで同じことをやっていますね。

`zip()`は、`python`で用意されている組み込み関数です、引数で与えられたイテラブルを一つにまとめたイテレータを返します。具体的には、

zip_example.py
```python
x = [1, 2, 3]
y = [4, 5, 6]
zipped = zip(x, y)
list(zipped)
[(1, 4), (2, 5), (3, 6)]
```
みたいな感じです。

つまり、`pairs`には、`secret`と`mask`の要素一つ一つの`chars`におけるインデックスが頭から順にペアになったイテレータが代入されるということです。

```python,linenos,linenostart=65
    cipher = ''.join(chars[(x + y) % len(chars)] for x, y in pairs)
```
joinは文字列の結合です。
```python
''.join()
```
というのは、各要素の間を''に、つまりスペースなしで結合するという宣言です。

要するに`cipher`には、`% len(chars)`によって`chars[]`の最大要素数を超えないようにしながら`pairs`の2要素を足した数をインデックスとした`chars`の値をどんどんつなげていた文字列が代入されるという感じですかね？どんだけバラバラにしたいんでしょうね、`cipher`というだけのことはありますね。

この`cipher`と、まだ正体のわかっていない`mask`を結合したものが`_mask_cipher_secret(secret)`の返り値です。

##### ③ _get_new_csrf_string()

次に、`_get_new_csrf_token()`のときに`_mask_cipher_secret()`の引数として渡していたり、`_mask_cipher_secret()`の中身でも登場したりする`_get_new_csrf_string()`も探してみます。

django/middleware/csrf.py
```python,linenos,linenostart=41
def _get_new_csrf_string():
    return get_random_string(CSRF_SECRET_LENGTH, allowed_chars=CSRF_ALLOWED_CHARS)
```

`CSRF_SECRET_LENGTH`は、

django/middleware/csrf.py
```python,linenos,linenostart=30
30 CSRF_SECRET_LENGTH = 32
```
と定義されていて、
`CSRF_ALLOWED_CHARS`は、前述のとおりアルファベット全てと数字0~9を全部含んだ文字列定数です。

`get_ramdom_string`は、別のファイルで定義されていました。

django/utils/cripto.py
```python,linenos,linenostart=52
 # RemovedInDjango40Warning: when the deprecation ends, replace with:
 #   def get_random_string(length, allowed_chars='...'):
def get_random_string(length=NOT_PROVIDED, allowed_chars=(
    'abcdefghijklmnopqrstuvwxyz'
    'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'
)):
    """
    Return a securely generated random string.
    The bit length of the returned value can be calculated with the formula:
       log_2(len(allowed_chars)^length)
    For example, with default `allowed_chars` (26+26+10), this gives:
      * length: 12, bit length =~ 71 bits
      * length: 22, bit length =~ 131 bits
    """
    if length is NOT_PROVIDED:
        warnings.warn(
            'Not providing a length argument is deprecated.',
            RemovedInDjango40Warning,
        )
        length = 12
    return ''.join(secrets.choice(allowed_chars) for i in range(length))
```

このようになっています。詳しく説明も書かれていてわかりやすいですね。要するに、

django/middleware/csrf.py
```python,linenos,linenostart=41
def _get_new_csrf_string():
    return get_random_string(CSRF_SECRET_LENGTH, allowed_chars=CSRF_ALLOWED_CHARS)
```

では、およそ180ビット長で`CSRF_ALLOWED_CHARS`の中からランダムでピックアップされた文字の文字列が返されるということです。


##### ④ まとめ

これらを踏まえて順に遡ってみます。

django/middleware/csrf.py
```python,linenos,linenostart=57
def _mask_cipher_secret(secret):
    """
    Given a secret (assumed to be a string of CSRF_ALLOWED_CHARS), generate a
    token by adding a mask and applying it to the secret.
    """
    mask = _get_new_csrf_string()
    chars = CSRF_ALLOWED_CHARS
    pairs = zip((chars.index(x) for x in secret), (chars.index(x) for x in mask))
    cipher = ''.join(chars[(x + y) % len(chars)] for x, y in pairs)
    return mask + cipher
```
これの
```python,linenos,linenostart=62
    mask = _get_new_csrf_string()
```
では、先ほど眺めた"およそ180ビット長で`CSRF_ALLOWED_CHARS`の中からランダムでピックアップされた文字の文字列が返される"関数を使って文字列を取得しています。
```python,linenos,linenostart=64
    pairs = zip((chars.index(x) for x in secret), (chars.index(x) for x in mask))
    cipher = ''.join(chars[(x + y) % len(chars)] for x, y in pairs)
    return mask + cipher
```
`mask`の正体がわかったところでもう一度見てみます。
(64): `secret`(引数)の文字列の各要素の`chars`(アルファベット、数字が順番に入ってる)におけるインデックスと、`mask`(ランダムにアルファベットが入ってるやつ)の各要素の`chars`におけるインデックスがzipによってまとめられて`pairs`とされて、
(65): `pairs`の要素二組を足した整数番目の`chars`の要素(`chars`の大きさで割ったあまりを入れておくことで`chars`の要素数を超えないようにしている)を、`pairs`の頭から最後まで順番に結合して文字列にしたものを`cipher`とし、
(66): (バラバラの)`mask`と、(もっとバラバラの)`cipher`を結合した文字列を返している、
といった具合です。注釈どおりのことをやっていますね。

これでようやく一番最初まで遡れます。

django/middleware/csrf.py
```python,linenos,linenostart=70
def _get_new_csrf_token():
    return _mask_cipher_secret(_get_new_csrf_string())
```

上の`_mask_cipher_secret` の引数`secret`に、`_get_new_csrf_string()`、つまりランダムに文字をピックアップして文字列として返すやつを指定しているという構造です。`mask`とは別の場所で呼ぶことで`mask`と`secret`が異なる文字列になるようにしているのかな？

以上が`CSRF_TOKEN`の構造です。ただランダムに文字列を生成しているのではなく、暗号らしくだいぶバラバラにしているのがわかったと思います。


ただHTML上でランダムな文字列を生成してそれをTOKENとするだけでは、乱数生成の手法によっては再現可能になってしまう危険性があるところを、Djangoでは`{% csrf_token %}`と記述するだけでこんな ~~面倒くさい~~ 堅牢そうな文字列をCSRF対策のトークンとして実装することができる、というわけです。本当に書き得ですね。


### 終わりに

便利ですねぇ

CSRFについては、僕も全然わかっていない部分があったりして、かなり適当な記述になってしまったので、補完してください。

### 参考
[https://docs.djangoproject.com/en/3.1/ref/csrf/](https://docs.djangoproject.com/en/3.1/ref/csrf/)

[http://www.htmq.com/html/input_hidden.shtml](http://www.htmq.com/html/input_hidden.shtml)

[https://github.com/django/django/blob/master/django/middleware/csrf.py](https://github.com/django/django/blob/master/django/middleware/csrf.py])

[https://medium-company.com/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA/](https://medium-company.com/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA/)


