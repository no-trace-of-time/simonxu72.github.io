---
layout: post
title:  "tmux的行对齐"
date:   2018-11-01 11:30:00 +8:00
categories: jekyll update
---
tmux启动之后，在有状态栏的情况下，发现总是对不齐。一回车换行，提示符PS1显示一行，输入的光标就移到下一行去了。

发现在$HOME/.tmux.conf配置文件中，增加一行：

{% highlight ruby %}
set -as terminal-overrides ',*:indn@'
{% endhighlight %}

有效。

经测试，在centos虚拟机里面ok的，但是，在wsl环境下，无效。

还需要继续研究下

另外，wsl上启动tmux的速度，比centos上慢多了。终究感觉还是原生虚拟机速度好些

