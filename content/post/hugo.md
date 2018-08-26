---
title: "Hugoでの記事アップロード手順"
date: 2018-08-26T10:24:40-04:00
draft: false
tags: ['hugo','日本語']
---

啓蒙と言うよりかはただ単なる自分のための備忘録です。
```.sh
cd Documents/
cd Github
cd blog
hugo new post/hugo.md
atom content/post/hugo.md
./deploy.sh
```
`md`ファイルの`draft`を`false`にしておくことを忘れずに。
