---
title: 'Centos Locale 错误: Failed to set locale, defaulting to C'
categories: D-Linux
translate_title: 'centos-locale-error:-failed-to-set-locale,-defaulting-to-c'
date: 2018-10-14 21:29:11
tags:
---
## 概述
今天新购了一台阿里云，在执行`yum`命令的时候，提示如下错误: `Failed to set locale, defaulting to C`。
折腾了好几遍，错误依然存在，同时原来的错误提示消失，执行命令显示一堆问号`????`。

## 解决方案
### 1.查看系统默认语言设置

```
┌─[root@bogon] - [~] - [2018-10-14 09:28:14]
└─[1] <> locale
LANG=zh_CN.utf8
LC_CTYPE="zh_CN.utf8"
LC_NUMERIC="zh_CN.utf8"
LC_TIME="zh_CN.utf8"
LC_COLLATE="zh_CN.utf8"
LC_MONETARY="zh_CN.utf8"
LC_MESSAGES="zh_CN.utf8"
LC_PAPER="zh_CN.utf8"
LC_NAME="zh_CN.utf8"
LC_ADDRESS="zh_CN.utf8"
LC_TELEPHONE="zh_CN.utf8"
LC_MEASUREMENT="zh_CN.utf8"
LC_IDENTIFICATION="zh_CN.utf8"
LC_ALL=zh_CN.utf8
```
### 2.查看当前区域设置

```
┌─[root@bogon] - [~] - [2018-10-14 10:23:46]
└─[0] <> localectl status
   System Locale: LANG=zh_CN.utf8
       VC Keymap: us
      X11 Layout: n/a
```

### 3.查看系统支持的汉语区域语言

```
┌─[root@bogon] - [~] - [2018-10-14 10:45:30]
└─[0] <> localectl list-locales| grep zh
zh_CN
zh_CN.gb18030
zh_CN.gb2312
zh_CN.gbk
zh_CN.utf8
zh_HK
zh_HK.big5hkscs
zh_HK.utf8
zh_SG
zh_SG.gb2312
zh_SG.gbk
zh_SG.utf8
zh_TW
zh_TW.big5
zh_TW.euctw
zh_TW.utf8
```

### 4.设置区域语言

```
┌─[root@bogon] - [~] - [2018-10-14 10:46:27]
└─[0] <> localectl set-locale  LANG=zh_CN.utf8
```

### 5.查看对应的配置文件

```
┌─[root@bogon] - [~] - [2018-10-14 10:47:14]
└─[0] <> cat /etc/locale.conf
LANG=zh_CN.utf8
SUPPORTED="zh_CN.UTF-8:zh_CN:zh:en_US.UTF-8:en_US:en"
SYSFONT="lat0-sun16"
```

### 6.更新profile

```
echo "export LC_ALL=zh_CN.UTF-8"  >>  /etc/profile
```

### 7.刷新bash 或者重启，使设置生效

## 参考
* [How to Set Up System Locale on CentOS 7](https://www.rosehosting.com/blog/how-to-set-up-system-locale-on-centos-7/)

