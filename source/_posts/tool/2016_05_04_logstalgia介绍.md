---
title: Logstalgia介绍
date: 2016-5-4 12:12
categories:
	- Misc
tags:
	- Misc
---
依赖于OpenGL

```
sudo apt-get install libglew-dev
sudo apt-get install libsdl-image1.2-dev
sudo apt-get install libboost-all-dev
sudo apt-get install libglm-dev
```
使用方法

```
ssh user@exapmle.com tail -f /var/log/example.com.log | logstalgia
```
