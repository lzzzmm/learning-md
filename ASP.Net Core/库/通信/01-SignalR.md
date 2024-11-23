# SignalR
SignalR用于实时双向通信，是一个\.NET Core/.NET Framework的开源实时框架. SignalR的可使用Web Socket, Server Sent Events 和 Long Polling作为底层传输方式。


## 1 不同通信协议
SignalR使用的三种底层传输技术分别是Web Socket, Server Sent Events 和 Long Polling。默认采用回落机制，从最优开始选择，如果不符合条件再往下选传输方式。也可以自己选择一种传输方式。

一旦建立连接, SignalR就会开始发送keep alive消息, 检查连接是否还正常. 如果有问题, 就会抛出异常。

### 1.1 WebSocket
一种全双工通信协议，允许客户端和服务器之间建立长时间的连接，进行实时、双向数据传输。

特点：
- 高效：通信过程中开销较小，适合高频率的实时通信
- 双向：支持服务器主动向客户端推送数据（全双工）
- 依赖：要求客户端和服务器都支持 WebSocket（需要现代浏览器和支持 WebSocket 的服务器）

SignalR 首选的传输方式，如果环境支持，SignalR 会优先使用 WebSocket

### 1.2 Server-Sent Events (SSE)
 一种单向通信协议，服务器可以通过 HTTP 向客户端推送数据，但客户端无法主动向服务器发送数据（需要单独的请求实现）。

特点：
- 单向：仅服务器向客户端推送数据
- 易用性：SSE 是基于标准的 HTTP 协议，浏览器支持良好（不支持 IE）
- 长连接：保持一个持续的 HTTP 连接，通过该连接发送数据流

当 WebSocket 不可用且客户端支持 SSE 时，SignalR 会使用它。

### 1.3 Long Polling
一种模拟实时通信的技术，客户端定期向服务器发送请求，服务器在有数据时才返回响应；如果没有数据，服务器会保持连接，直到超时。

特点:
- 模拟实时：本质上是 HTTP 请求，但通过延迟响应来实现实时效果
- 开销大：频繁的 HTTP 请求和连接建立，效率低于 WebSocket
- 兼容性好：适用于几乎所有浏览器和服务器环境

当 WebSocket 和 SSE 都不可用时，SignalR 会回退到 Long Polling。



![2024-11-23-02-08-01.png](./images/2024-11-23-02-08-01.png)