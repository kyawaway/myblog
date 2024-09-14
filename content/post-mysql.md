+++
title = "MySQLの呼び出しでハマった"
template = "page.html"
date = 2024-09-14T16:00:00Z
[taxonomies]
tags = ["web", "mysql"]
[extra]
summary = "version 9.0で'mysql_native_password'が廃止された"
mathjax = "tex-mml"
+++

## 環境

macOS 14.3.1, MySQL 9.0, Python 3.9.8

## 起こったこと

Pythonからmysqlclientを呼ぼうとしたら，

```bash
MySQLdb.OperationalError: (2059, "Authentication plugin 'mysql_native_password' cannot be loaded ...
```

などと怒られてしまった．

## 解決策

MySQLのバージョンが9.0になってから，ネイティブ認証プラグイン(`mysql_native_password`)が廃止されたらしい．

* [MySQL 9.0 – it’s time to abandon the weak authentication method](https://blogs.oracle.com/mysql/post/mysql-90-its-time-to-abandon-the-weak-authentication-method)

つまり，(緊急の場合は)MySQLのバージョンを下げれば良い．

```bash
brew uninstall mysql
brew install mysql@8.4
ln -s /opt/homebrew/opt/mysql@8.4/bin/mysql /usr/local/bin/mysql
```

これで直った．
