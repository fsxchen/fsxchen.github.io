---
title: mysql出现Lock-Wait-Timeout
date: 2016-4-21 17:17
categories:
	- Misc
tags:
	- MySQL
---
# mysql出现Lock Wait Timeout

使用的RDS,出现了Lock Wait Timeout的问题
解决方法

以ROOT用户登录数据库后执行
`SHOW FULL PROCESSLIST`,然后`KILL <id>`
