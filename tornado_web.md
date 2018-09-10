#### Django的部署方式
以django为代表的python web服务器部署时候采用wsgi协议和服务器对接（被服务器托管），这类服务器都是多线程的，每一个网络请求都会有一个对应的线程来
用web应用（如Django）来进行处理

#### 两种不适用的应用场景
1. 用户量大，高并发
  瞬时高并发，导致web应用线程和进程数量不够，或者服务器处理不了很多访问量，没有分发时间
2. 大量的HTTP持久连接
  短链接： 一个请求接收到处理完发送给客户端时候，连接传输层就断开了
  长链接： 发送响应给客户端后，连接不断开
  对于HTTP 1.0 可以在Header中添加 Connection：Keep-Alive
  对于HTTP 1.1  所有的连接默认都是持久连接
  如果有很多持久连接，在Nginx的最大可连接数中设置，默认1024个连接
  

**tornado是web服务器兼Web应用框架**
tornado支持WebSockets
