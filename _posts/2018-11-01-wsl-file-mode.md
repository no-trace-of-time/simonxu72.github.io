---
layout: post
title:  "wsl下设置windows磁盘的文件模式属性"
date:   2018-11-01 11:58:00 +8:00
categories: jekyll update
---
wsl下访问windows的分区，比如C:,D:盘，上面的文件属性，在wsl下看，都是777，还没法用chmod修改。chmod命令执行后无效。这个对git造成影响，用visual studio code编辑的文件，最后提交到库里面，属性都不对了。

研究后，发现可以编辑/etc/wsl.conf文件，添加下面的行
{% highlight ruby %}
[automount]
options = "metadata"
{% endhighlight %}

重新启动后（其实应当只要重启wsl的实例就行），发现生效了

{% highlight ruby %}
cd /mnt/c
touch a
ls -l a

{% endhighlight %}

目前的问题：
** vscode在保存新文件后，默认的模式就是777，需要能设置为644