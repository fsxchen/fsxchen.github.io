---
title: with和contextlib
date: 2016-3-28 11:12
category:
	- Python
tags:
	- Python
---
# with和contgextlib

## 应用场景

即使运行失败也要退出,比如:

* 关闭一个文件
* 释放一个锁
* 创建一个临时的代码补丁
* 在特殊环境中运行受保护的代码

## with语句和上下文协议
最常用的是打开和关闭一个文件

```
from __future__ import with_statement
# 在2.5 以及2.5之前,需要这样引入
with open('/etc/hosts') as fd:
    for line in fd
```
实现with的协议如下:

```
class Context(object):
	def __enter__(self):
		print("entering the zone")
	def __exit__(self, exception_type, exception_value,
					exception_traceback):
		print('leaving the zone')
		if exception_type is None:
			print('no error')
		else:
			print("with an error (%s)" % exception_value)

with Context():
	print("i an the zone")
```
output
```
entering the zone
i an the zone
leaving the zone
no error
[Finished in 0.1s]
```
在py中,一个类实现两个方法 `__enter__`和`__exit__`,就实现了with协议,`thread`和`threading`模块的一些类也实现了这些方法
thread.LockType
threading.Lock
threading.RLock
threading.Condition
threading.Semaphore
threading.BoundedSemaphore

## contextlib模块
给with提供了一个辅助类,包含了一yield分开的`__enter__`和`__exit__`,所以上面的离职可以写成
```
from contextlib import contextmanager
# from __future__ import with_statement

@contextmanager
def context():
	print("entering the zone")
	try:
		yield
	except Exception, e:
		print("with an error (%s) % e")
		raise e
	else:
		print("with no error")

with context():
	print("starting")
```
## 上下文实例
