---
title: "Bluelight"
date: 2018-09-04T17:34:10-04:00
draft: false
tags: ['English','tech']
---

In the night mode of Oneplus 3, the links in blue are not visible. I think they may be completely cutting the bluelight. I had been using a blue theme for this blog but decided to change to green in light of this. To change the theme color in hugo even, go to `blog/themes/even/src/css/_variable.scss`, choose theme there, and run the following.
```.sh
cd blog/themes/even/src/
npm install
npm run build
```