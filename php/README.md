# PHP

#### php-fpm是什么

#### php-fpm 有几种运行方式 分别适用于什么场景？

#### 已知Nginx和PHP-FPM安装在同一台服务器上，Nginx连接PHP-FPM有两种方式：一种是类似127.0.0.1:9000的TCP socket；另一种是类似/tmp/php-fpm.sock的Unix domain socket。请问如何选择，需要注意什么。
Unix domain socket的流程不会走到TCP 那层，直接以文件形式，以stream socket通讯。如果是TCP socket,则需要走到IP层。说的通俗一点，追求可靠性就是tcp（需要占用一个端口，更稳），追求高性能就是Unix Socket（不需要占用端口，更快）

拓展阅读：[nginx、php-fpm默认配置与性能–TCP socket还是unix domain socket](https://www.cnxct.com/default-configuration-and-performance-of-nginx-phpfpm-and-tcp-socket-or-unix-domain-socket//)

#### 手动用php实现一个单例

#### php5和7有什么区别，增加了哪些特性又去除了哪些？

#### 静态延迟绑定是什么?

#### 依赖注入原理

#### php5和php7的gc区别

#### 你在尝试过PHP的多进程编程吗？进程和线程有什么区别？

#### 你在平时开发中对MVC有什么理解？Logic或者Service层呢？

#### PHP多线程，线程之前如何共享数据


### php mysql扩展中的connet和pconnet有什么区别

### php的gc流程是什么样的,unset后会马上回收内存吗? 假如有非常大的数组,可能会超过内厝最大限制,你会怎么优化?
