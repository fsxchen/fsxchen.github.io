---
title: Window折腾Python
date: 2016-4-9 18:41
categories:
	- Python
tags:
	- Python
---

# Windows下折腾python

## Unable to find vcvarsall.bat


命令行下执行
```
SET VS90COMNTOOLS=%VS100COMNTOOLS%
```
如果你安装的是 2012 版
```
SET VS90COMNTOOLS=%VS110COMNTOOLS%
```
如果你安装的是 2013版
```
SET VS90COMNTOOLS=%VS120COMNTOOLS%
```
或者更暴力，直接配置系统环境变量 VS90COMNTOOLS指向 %VS你的版本COMNTOOLS%

你还可以更暴力，在`..python安装路径...\Lib\distutils目录下有个msvc9compiler.py`找到243行  

`toolskey = "VS%0.f0COMNTOOLS" % version`   直接改为 `toolskey = "VS你的版本COMNTOOLS"(这个就是为什么要配 ”VS90COMNTOOLS“ 的原因，因为人家文件名都告诉你了是  Microsoft vc 9的compiler,   代码都写死了要vc9的comntools，就要找这个玩意儿，找不到不干活)`

这么做的理由是`Python2.7` 扩展包是可以用08版或者更高的VS编译的，其`setup.py`(安装脚本)都是去windows系统寻找08版的VS,所以设置VS90的path

如果Python版本小于2.7，强烈建议使用 VS08版，用2010或者更高可能部分扩展不好使。给个例子：


在安装一些Python模块时，大部分是cpython写的模块时会发生如下错误 `error: Unable to find vcvarsall.bat`先前的一篇文章：在Windows上安装Scrapy时也讲到了这个问题。当时讲到的方案是，安装VS 2008进行解决，但是Vs 2008又太大，不想装，所以这次想到了另外的方案，同样是上次说的，当时上次很不完整。
解决方案一：安装Vs2008（实测）
完全的无脑流，安装完问题直接解决。
解决方案二：安装Vs2010（未测试）
上次在电脑上装个Vs2010并不能像 vs2008那样直接解决问题，从网上找到如下解决方案，不知是否可行。
打开`<python安装目录>\Lib\distutils\msvc9compiler.py`
找到 `toolskey = "VS%0.f0COMNTOOLS" % version`，直接修改为 `toolskey = "VS100COMNTOOLS"`
解决方案三：安装MinGW（实测）
### 1、下载安装MinGW，下载地址为：[MinGW](http://sourceforge.net/projects/mingw/files/latest/download?source=files)
### 2、在`MinGW`的安装目录下找到`bin`文件夹，找到`mingw32-make.exe`，复制一份更名为`make.exe`
### 3、把`MinGW`的路径添加到环境变量`path`中，比如我把M`inGW`安装到`D:\MinGW\`中，就把`D:\MinGW\bin`添加到`path`中；
### 4、在`<python安装目录>\distutils`增加文件`distutils.cfg`，在文件里输入
```
[build]
compiler=mingw32
```
保存；
### 5、执行原先的模块安装，发现还是报错，报错内容为
```
：error: command ‘gcc’ failed: No such file or directory  
```
解决方案是将`D:\MinGW\lib`再添加到PATH中。

### 6、如果安装过程中出现 error: Could not find ‘openssl.exe’ 则直接到http://pypi.python.org/pypi/pyOpenSSL/0.13 下载安装即可。
### 7、再次执行时安装模块时，发现如下错误：
```
D:\MinGW\bin\gcc.exe -mno-cygwin -mdll -O -Wall “-ID:\Program Files\Python27\inc
lude” “-ID:\Program Files\Python27\include” “-ID:\Program Files\Python27\PC” -c
../libdasm.c -o build\temp.win32-2.7\Release\..\libdasm.o
cc1.exe: error:unrecognized command line option ‘-mno-cygwin’
error: command ‘gcc’ failed with exit status 1
```
原因是`gcc 4.6.x` 以后不再接受`-mno-cygwin`为了解决这个问题需要修改`<python安装目录>\distutils\cygwinccompiler.py`文件。找到：

```
    self.set_executables(compiler=&#039;gcc -mno-cygwin -O -Wall&#039;,
                                compiler_so=&#039;gcc -mno-cygwin -mdll -O -Wall&#039;,
                                compiler_cxx=&#039;g++ -mno-cygwin -O -Wall&#039;,
                                linker_exe=&#039;gcc&#039;,
                                linker_so=&#039;%s -mno-cygwin %s %s&#039;
                                           % (self.linker_dll, shared_option,
                                              entry_point))
```
   修改为：

```
    self.set_executables(compiler=&#039;gcc -O -Wall&#039;,
                                compiler_so=&#039;gcc -mdll -O -Wall&#039;,
                                compiler_cxx=&#039;g++ -mno-cygwin -O -Wall&#039;,
                                linker_exe=&#039;gcc&#039;,
                                linker_so=&#039;%s -mno-cygwin %s %s&#039;
                                           % (self.linker_dll, shared_option,
                                              entry_point))
```
   an

## No module named Crypto.PublicKey
明明安装了
```
pip install pycrypto
```
解决方法：
We should rename crypto directory under “Lib/site-packages” to Crypto
因为windows是不区分大小写的，但是py加载模块区分。
