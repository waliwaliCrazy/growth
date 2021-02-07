<h1> Go 发送 http 请求: post，get，put，delete请求示例代码 </h1>

---

- [HTTP 请求方法](#http-请求方法)
- [请求示例](#请求示例)
  - [GET](#get)
  - [POST](#post)
    - [kv 形式](#kv-形式)
  - [PUT](#put)
  - [PATCH](#patch)
  - [DELETE](#delete)
- [源码参考](#源码参考)
---

# HTTP 请求方法

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

在日常开发中大多数会用到 5 种请求方法： GET、POST、PUT、PATCH 和 DELETE

方法 | 描述
---|---
GET | 请求指定的页面信息，并返回实体主体。
POST | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。
PUT | 从客户端向服务器传送的数据取代指定的文档的内容。
DELETE | 请求服务器删除指定的页面。
PATCH |	是对 PUT 方法的补充，用来对已知资源进行局部更新。

# 请求示例

## GET

**http.Get 直接访问**

```go
import (
	"net/http"
)

response, err := http.Get("https://blog.csdn.net/zyndev")
```

这种方法适合不需要 header 的方式

**自定义参数访问**

```go
import (
	"net/http"
)

url := "https://blog.csdn.net/zyndev"

req, _ := http.NewRequest("GET", url, nil)

req.Header.Add("Authorization", "xxxx")

response, err := http.DefaultClient.Do(req)
```

这种方法适合需要自定义一些 header 的场景

在查看 `http.Get` 方法源码时, 可以看出其是一个简便使用方式

```go
func (c *Client) Get(url string) (resp *Response, err error) {
	req, err := NewRequest("GET", url, nil)
	if err != nil {
		return nil, err
	}
	return c.Do(req)
}
```

## POST

在 POST 方式一般常用的为 2 中， 
1. 通过 kv 形式传送，例如 `form-data` 和 `x-www-form-urlencoded`
2. 通过 json 形式传送，例如 `application/json`

### kv 形式

```go
import (
	"net/http"
    "strings"
)

url := "https://blog.csdn.net/zyndev"

payload := strings.NewReader("a=111")

response, err := http.Post(url, "application/x-www-form-urlencoded", payload)
```

除了通过 http.Post 还可以使用 http.PostForm

```go

import (
	"net/http"
	"net/url"
)

targetUrl := "https://blog.csdn.net/zyndev"

payload := url.Values{"key":{"value"}, "id": {"123"}}

response, err := http.PostForm(targetUrl, payload)
```


## PUT
## PATCH
## DELETE

# 源码参考