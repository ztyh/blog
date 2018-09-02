---
title: "Markdown続編"
date: 2018-08-30T10:42:13-04:00
draft: false
tags: ['markdown','日本語']
---

[ここ](https://gist.githubusercontent.com/rt2zz/e0a1d6ab2682d2c47746950b84c0b6ee/raw/83b8b4814c3417111b9b9bef86a552608506603e/markdown-sample.md)に書いてあることを試しました。

空行で段落。

**太字**。`**`で囲んでます。斜体がなぜかだせない。

h1タイトル
----------
----------
下に
```
----------
----------
```
を書いたら文字が大きくなって横線がたされました

---どうなるかな---　ハイフン３つで囲んでます

12--14　ハイフン２つです

点３つで...　ちょっと圧縮された

h2タイトル
-------
下に
```
-------
```
を書いたら文字が大きくなりました

 1. 金
 2. 銀
 3. 銅

スペース、数字、点、スペースって入力してます

スペース四個インデントでコード

    # Let me re-iterate ...
    for i in 1 .. 10 { do-something(i) }

波線３つ`~~~`で囲むのもOK

~~~
define foobar() {
    print "Welcome to flavor country!";
}
~~~

注釈[^1]は`[^1]`などで場所指定。また別に`[^1]:`に続けて注釈内容を記入する。

[^1]: 注釈

***

`***`で横線上に書きました

前のと結構かぶったな。難しいのは飛ばした。その他はまたの機会に。