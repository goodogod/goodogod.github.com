---
layout: default
title: 讓 jekyll 看懂 github flavored markdown
---
<h2>{{ page.title }}</h2>
昨天嘗試使用 github flavored markdown 語法:

>
```
vi bomfile
:set nobomb
:wq
```
>

提交後，github 表示看不懂，原因是此代碼標記不是標準的 markdown ，而是稱為 redcarpet 的變種 markdown。

在 Jekyll 0.12.1 之前，需使用 [redcarpet2 jekyll plugin](https://github.com/nono/Jekyll-plugins) ，現在只需要在 _config.yml 增加一行：

```
markdown: redcarpet
```

接著在本地運行試試：

```
jekyll serve
```

錯誤拜拜！