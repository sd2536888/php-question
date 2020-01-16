# Nginx

#### Nginx如何支持高并发？

Nginx: 采用单线程来异步非阻塞处理请求(管理员可以配置Nginx主进程的工作进程的数量)(epoll)，不会为每个请求分配cpu和内存资源，节省了大量资源，同时也减少了大量的CPU的上下文切换。所以才使得Nginx支持更高的并发。


#### 502报错可能原因有哪些？
(1) FastCGI进程是否已经启动

(2) FastCGI worker进程数是否不够

(3) FastCGI执行时间过长

(4) FastCGI Buffer不够

(5) Proxy Buffer不够

(6) php脚本执行时间过长


#### FastCGI是什么？


#### 如何实现反向代理？


#### fastcgi与cgi的区别
cgi每次都会fork一个进程来处理请求，请求结束后退出进程。fastcgi是在web服务器启动时就已经开启了，而且不会退出



### nginx能做4层流程转发吗?和7层有什么区别

### nginx支持哪些负载均衡分配算法


### nginx如何和php-fpm通信
