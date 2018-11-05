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

## vscode在保存新文件后，默认的模式就是777，需要能设置为644

根据[微软2018/04的Windows 10更新][chmod-chown-wsl-improvements]，使用windows上的编辑器（比如VSCode）编辑文件，会清除文件的linux metadata。
这时，文件会恢复到默认的权限。

{% highlight ruby %}
There are a few things to make sure you're aware of when tinkering with the new metadata:

    1.Editing a file using a Windows editor may remove the file's Linux metadata. In this case, the file will revert to its default permissions
    2.Removing all write bits on a file in WSL will make Windows mark the file as read-only.
    3.If you have multiple WSL distros installed or multiple Windows users with WSL installed, they will all use the same metadata on the same files. The uid's of each WSL user account might differ. This something to consider when setting permissions.
{% endhighlight %}


那么，我们需要设置/mnt/c的默认权限？

增加wsl.conf文件中的options内容，umask=022看看结果
重新启动后，vscode新创建的文件，wsl下权限变为：755，是起作用了！
把umask修改为133，应当符合基本要求了

## 为了方便测试，需要重新启动wsl实例，但是不启动windows

{% highlight ruby %}
net stop LxssManager
net start LxssManager
{% endhighlight %}

重新打开终端，vscode创建新文件，默认文件模式变成了755。普通文件ok了，但是，所有C：盘上的目录模式都没有了X，导致无法进入对应目录。
看来还是只能是022

{% highlight ruby %}
[automount]
options = "metadata,umask=022"
{% endhighlight %}

## wsl.conf的参考内容在[这里][automatically-config-wsl]

[chmod-chown-wsl-improvements]: https://blogs.msdn.microsoft.com/commandline/2018/01/12/chmod-chown-wsl-improvements/
[automatically-config-wsl]: https://blogs.msdn.microsoft.com/commandline/2018/02/07/automatically-configuring-wsl/