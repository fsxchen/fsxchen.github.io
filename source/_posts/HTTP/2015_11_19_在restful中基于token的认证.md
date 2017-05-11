---
title: 在RESTFul中基于Token的认证
date: 2015-11-19 15:24
categories: 
	- Misc
tags: 
	- restful
	- http
---
# 在RESTFul中使用Token来进行身份认证

用户只登录一次
下次访问的时候使用cookie而不是username或者是密码

失效

```sh
curl -c c.txt \
    http://localhost:8080/api/v1/login \
    -u user1:pass1

curl -b c.txt \
    --data '{titl...}' \
    http://localhost:8080/api/v1/poems
```

logging in

```
def GET(self):
    user = require_authenticated_user(
        self.db)
    token = generate_token()
    self.db.tokens[tooken] = {"user": user}

```
