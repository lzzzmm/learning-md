# .http文件
.http 文件是用于发送 HTTP 请求并查看响应的工具。可以通过这种方式直接在 Rider 内部发起 HTTP 请求，而无需依赖外部的 API 测试工具（例如 Postman）。这些请求可以用来测试 API，验证 RESTful 接口，或进行其他形式的 HTTP 调试。


## 1 语法介绍
### 1.1 注释
```http
# 注释
// 注释
```

### 1.2 变量
```http
@hostName = localhost
@port = 5000
@host = {{hostname}}:{{port}}
```

### 1.3 请求
`HTTPMethod URL [HTTPVersion]`

- HTTPMethod:
    - GET
    - HEAD
    - POST
    - PUT
    - PATCH
    - DELETE
    - TRACE
    - CONNECT

- URL:发起 HTTP 请求的 URL 地址
- HTTPVersion 可选，指定应使用的 HTTP 版本，即 HTTP/1.1、HTTP/1 或 HTTP/3

一个 .http 文件中包含多个请求时使用 ### 作为分隔符，建议每个请求下都加上 ###，表示一个完整的请求块。

```http
###
POST https://localhost:7273/api/EasyTest/a1
###
POST https://localhost:7273/api/EasyTest/a2
###
```

### 1.4 请求头
请求头的语句紧接在请求行的后面一行，请求行和请求头之间不能包含空白行，请求头之间也不能有空白行。
```http
###
POST https://localhost:7273/api/EasyTest/a

Accept: application/json
Content-Type: application/json
Authorization：Bearer eyJhbGciOiJIUzI1NiIsInR5c...
###
```

### 1.5 请求中文
在请求头后面添加请求正文，需要与请求头间隔一行。
```http
POST https://localhost:7273/api/EasyTest/a
Content-Type: application/json

{}
```


