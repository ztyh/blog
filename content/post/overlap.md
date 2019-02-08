
---
title: "Pythonのreで重複マッチ"
date: 2018-01-15T18:57:52-05:00
draft: false
tags: ['日本語','python','学習']
---

以下をご覧になってください。

<script src="https://gist.github.com/ztyh/c411ecfd41dac44453ac48f3de21204f.js"></script>

[これ](https://youtu.be/K8L6KVGG-7o)見てたのですが、普通にマッチを探そうとしても三つしか見つけてくれない事に気づきました。調べたら[これ](https://stackoverflow.com/questions/5616822/python-regex-find-all-overlapping-matches)で解決できたのですが味噌は`(?=...)`と`()`で作るグループと`group(1)`です。普通マッチが見つかればマッチしたものは検索する文字列から取り除かれます。しかし`?=`を使う場合はそうではありません。
