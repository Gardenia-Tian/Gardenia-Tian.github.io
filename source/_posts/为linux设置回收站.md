---
title: 为linux设置回收站
date: 2022-11-21 11:12:03
tags: [Linux, 系统管理]
categories: [清浅录]
cover: https://ts1.cn.mm.bing.net/th/id/R-C.958dfd89335c457ac3916a3874a0cc97?rik=3hj8iuWAzDhHCw&riu=http%3a%2f%2fimg.aiimg.com%2fuploads%2fallimg%2f150527%2f280082-15052G05152.jpg&ehk=5BcTxGdDzwrborjiw%2b5Fpj34Y7eyHyc19fgRChPG4YI%3d&risl=&pid=ImgRaw&r=0
---

# 为Linux设置回收站

昨天手快误删了一个很重要的文件夹, 经过一系列的操作最后发现确实是找不回来了, 所以痛定思痛一定要为Linux搭建一个回收站, 参考了网上很多教程, 现在来记录一下搭建过程.

## 原理

原理其实很简单, 就是自定义一个回收站文件夹, 然后将删除指令自定义成将文件移动到回收站里, 再设置一个定时来定期清空回收站文件夹.

## 操作流程

### 创建回收站文件夹

我把回收站创建在我的账号的根目录下了, 并且希望平时隐藏, 所以指令如下

```shell
mkdir ~/.trash
```

###  回收站相关的命令进行定义

```shell
vim ~/.bashrc_trash
```

创建好`bashrc_trash`文件之后, 在里面添加如下内容

```shell
# 为rm重定位为trash的命令, 当执行rm的时候自动执行trash函数
alias rm=trash
# 同上
alias r=trash
# 列出回收站的内容
alias rl='ls ~/.trash'
# 撤销删除
alias ur=undelfile

# 撤销删除, 就是将回收站中的内容移动回去
undelfile()
{
 mv -i ~/.trash/\$@ ./
}

# 删除, 就是将当前文件夹移到回收站里, 注意mv指令没有-r参数, 所以使用的时候不用rm -rf, 直接rm -f或者rm就可以
trash()
{
 mv $@ ~/.trash/
}

# 清空回收站, 添加确认操作
cleartrash()
{
 read -p "clear sure?[n]" confirm;
 [ $confirm == 'y' ] || [ $confirm == 'Y' ] && /bin/rm -rf ~/.trash/*
}

# 不需要确认的清空回收站, 用于定时清空, 事实上也可以给cleartrash()配一个参数, 这个以后要是有时间可以再搞一下
CLEARTRASH()
{
 /bin/rm -rf ~/.trash/*
}
```

### 将自定义的指令添加到.bashrc

```shell
vim ~/.bashrc
```

打开`~/.bashrc`之后在其中添加如下指令

```shell
# add trash
if [ ! -f "~/.bashrc_trash" ]; then
    . ~/.bashrc_trash
fi
```

这样每次启动一个终端就会自动加载我们自定义的指令

### 定期清空文件夹

其实到上一步就已经可以使用回收站了, 但是我们希望回收站更完善一点, 能够定时清空回收站, 这样就不用我们手动管理回收站中的内容了, 所以再设置一个定时清空功能. 这个功能要用到`crontab `指令, Linux `crontab` 是用来定期执行程序的命令, `-e`参数可以执行文字编辑器来设定时程表。首先输入如下指令

```shell
crontab -e
```

 之后会进入到/tmp/crontab.xFcuCa/crontab, 这个如果不指定用户默认是为自己的用户配置的.

之后在里面添加

```shel
0 0 * * 0 CLEARTRASH
```

这句话的含义是每周日零点清空回收站, 前面的五位数字用来指定时间, 含义如下

```
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

那么到此位置我们回收站的配置就完成了, 其实还有更好的方式, 可以让回收站定期清空指定日期以前的数据, 这样安全性会更好一点, 如果以后有时间, 我们就再折腾一下那个方案, 现在的版本也可以实现一个较为安全的`rm`操作, 妈妈再也不用担心我手快啦!
