# HTTP状态码大全

HTTP 状态码 HTTP Status Code

---

- [HTTP状态码大全](#http状态码大全)
- [1xx消息](#1xx消息)
- [2xx成功](#2xx成功)
- [3xx重定向](#3xx重定向)
- [4xx客户端错误](#4xx客户端错误)
- [5xx服务器错误](#5xx服务器错误)
- [参考](#参考)

---

HTTP 状态码的英文为 HTTP Status Code。 

下面是常见的HTTP状态码：

- 200 请求成功
- 301 资源（网页等）被永久转移到其它URL
- 302 资源（网页等）被临时转移到其它URL
- 401 用户没有必要的凭据
- 403 服务器已经理解请求，但是拒绝执行它
- 404 请求的资源（网页等）不存在
- 405 请求行中指定的请求方法不能被用于请求相应的资源
- 406 请求的资源的内容特性无法满足请求头中的条件，因而无法生成响应实体，该请求不可接受
- 408 请求超时
- 500 内部服务器错误
- 502 作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应
- 503 由于临时的服务器维护或者过载，服务器当前无法处理请求
- 504 作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器收到响应

分类 | 描述
---|---
1**	| 信息，服务器收到请求，需要请求者继续执行操作
2**	| 成功，操作被成功接收并处理
3**	| 重定向，需要进一步的操作以完成请求
4**	| 客户端错误，请求包含语法错误或无法完成请求
5**	| 服务器错误，服务器在处理请求的过程中发生了错误

# 1xx消息

这一类型的状态码，代表请求已被接受，需要继续处理。这类响应是临时响应，只包含状态行和某些可选的响应头信息，并以空行结束。由于HTTP/1.0协议中没有定义任何1xx状态码，所以除非在某些试验条件下，服务器禁止向此类客户端发送1xx响应。这些状态码代表的响应都是信息性的，标示客户应该采取的其他行动。

- 100 Continue
- 101 Switching Protocols
- 102 Processing

# 2xx成功

- 200 OK
- 201 Created
- 202 Accepted
- 203 Non-Authoritative Information
- 204 No Content
- 205 Reset Content
- 206 Partial Content
- 207 Multi-Status

# 3xx重定向

- 300 Multiple Choices
- 301 Moved Permanently
- 302 Move Temporarily
- 303 See Other
- 304 Not Modified
- 305 Use Proxy
- 306 Switch Proxy
- 307 Temporary Redirect

# 4xx客户端错误

- 400 Bad Request
- 401 Unauthorized
- 402 Payment Required
- 403 Forbidden
- 404 Not Found
- 405 Method Not Allowed
- 406 Not Acceptable
- 407 Proxy Authentication Required
- 408 Request Timeout
- 409 Conflict
- 410 Gone
- 411 Length Required
- 412 Precondition Failed
- 413 Request Entity Too Large
- 414 Request-URI Too Long
- 415 Unsupported Media Type
- 416 Requested Range Not Satisfiable
- 417 Expectation Failed
- 418 I'm a teapot
- 421Misdirected Request
- 422 Unprocessable Entity
- 423 Locked
- 424 Failed Dependency
- 425 Too Early
- 426 Upgrade Required
- 449 Retry With
- 451 Unavailable For Legal Reasons


# 5xx服务器错误

- 500 Internal Server Error
- 501 Not Implemented
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout
- 505 HTTP Version Not Supported
- 506 Variant Also Negotiates
- 507 Insufficient Storage
- 509 Bandwidth Limit Exceeded
- 510 Not Extended
- 600 Unparseable Response Headers

# 参考

- https://baike.baidu.com/item/HTTP%E7%8A%B6%E6%80%81%E7%A0%81
- https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81