---
layout: default
title: Use .bashrc at Mac OS
---
<h2>{{ page.title }}</h2>

假如我要新增 `alias ll='ls -l'` ，到 `.bashrc` ，按照 Linux 慣例，先到 ~/.bashrc 下新增一筆：

{% highlight bash %}
alias ll='ls -l
{% endhighlight %}

重啟終端機後發現依然沒有作用，原因是，在 Mac OS X 還必須修改 `~/.bash_profile` ：

```bash
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

重啟終端機後可看到效果。