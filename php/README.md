# PHP

### 分别说下cgi,php-cgi,fastcgi,php-fpm是什么?

* cgi是一个**协议**,保证web server传递过来的数据是标准格式的
* php-cgi是**PHP的解释器**。php-cgi只是个CGI程序，他自己本身只能解析请求，返回结果，不会进程管理
* Fastcgi是CGI的更高级的一种方式，是用来**提高CGI程序性能**的
* php-fpm是调度php-cgi进程的程序


拓展阅读：[说说http/webserver/fastcgi/php-fpm](http://www.lxlxw.me/?p=216)

[如何通俗地解释 CGI、FastCGI、php-fpm 之间的关系？](https://www.zhihu.com/question/30672017)

[搞不清FastCgi与PHP-fpm之间是个什么样的关系](https://segmentfault.com/q/1010000000256516)

### php-fpm 有几种运行方式 分别适用于什么场景？
这个是 php-fpm.conf 配置文件中```pm``` 控制项，主要是采用何种方式来**控制子进程数量**，主要有**3种方式**：
* **static:静态模式**,固定有n个子进程,适用于内存较大的服务器上,减少频繁增加/减少子进程的开销
* **dynamic:动态模式**,至少有1个，启动```pm.start_servers```个，最多可以启动``` pm.max_children```个。如果子进程有空闲，且个数小于 ```pm.min_spare_servers```，则补齐到```pm.min_spare_servers```，如果大于 ```pm.max_spare_servers```，则降低到 ```pm.max_spare_servers```。这种模式在**正式环境中**使用比较多
* **ondemand:按需启动**,没有请求时没有子进程，有请求时启动。最多可以启动```pm.max_children```个，如果有空闲，子进程经过```pm.process_idle_timeout``` 后会被杀掉，最终没有任何子进程。这种模式**使用最少**，主要是节省服务器资源，空闲后首次启动，响应较慢。

拓展阅读：[PHP php-fpm 有哪些子进程运行方式？](http://www.jishuchi.com/read/php-interview/2712)

[PHP-FPM及其三种运行方式
](https://blog.csdn.net/njrclj/article/details/85062459)


#### 已知Nginx和PHP-FPM安装在同一台服务器上，Nginx连接PHP-FPM有两种方式：一种是类似127.0.0.1:9000的TCP socket；另一种是类似/tmp/php-fpm.sock的Unix domain socket。请问如何选择，需要注意什么。
Unix domain socket的流程不会走到TCP 那层，直接以文件形式，以stream socket通讯。如果是TCP socket,则需要走到IP层。说的通俗一点，追求可靠性就是tcp（需要占用一个端口，更稳），追求高性能就是Unix Socket（不需要占用端口，更快）

拓展阅读：[nginx、php-fpm默认配置与性能–TCP socket还是unix domain socket](https://www.cnxct.com/default-configuration-and-performance-of-nginx-phpfpm-and-tcp-socket-or-unix-domain-socket//)

#### 手动用php实现一个单例
```
<?php

class Singleton {
    // 私有化构造方法
    private function __construct()
    {

    }

    // 私有化clone方法
    private function __clone()
    {

    }


    // 保存实例的静态对象
    public static $singleInstance;

    /**
     * 声明静态调用方法
     * 目的：保证该方法的调用全局唯一
     */
    public static function getInstance()
    {
        if (!self::$singleInstance) {
            self::$singleInstance = new self();
        }

        return self::$singleInstance;
    }
}
```

#### php5和php7有什么区别，增加了哪些特性又去除了哪些？
主要区别:
* 性能:比5.6提升2倍
* 全面支持64位
* PHP7.0之前出现的致命错误，都改成了抛出异常
* 新增了函数的返回类型声明
* 新增了匿名类
* 新增了新的运算符```<=>```被称为“太空飞船运算符”
* 移除了一些扩展,比如mysql,mssql
* 移除了一些sapi,比如apache,isapi

扩展阅读:[WHAT ARE THE MAJOR DIFFERENCES BETWEEN PHP 5 AND PHP 7?](https://www.freelancinggig.com/blog/2018/04/23/major-differences-php-5-php-7/)

[PHP 7 修改了什么呢](https://segmentfault.com/a/1190000012507565)

#### 静态延迟绑定是什么?

#### 依赖注入原理

#### php5和php7的gc区别

#### 你在尝试过PHP的多进程编程吗？进程和线程有什么区别？
首先，PHP是可以操作多进程的，这个模块叫做pcntl，一般情况下，会被默认安装，可以通过php -m来查询。

PHP也可以操作多线程，需要一个叫做pthread的库来实现，但是PHP的pthread比较奇怪，变量一直不能共享，似乎失去了多线程的意义。

相对来说，在生产环境更具备价值的是php的多进程。

一般收来是如下几个函数：
pcntl_fork，这个函数就相当于linux系统下的fork系统调用，用来fork出一个新的进程。
pcntl_wait或者pcntl_waitpid，这个函数是用来防止僵尸进程的。
pcntl_exe相当于linux下的exec，就是在当前进程中直接执行另外一个进程，比如利用php进程直接呼起一个go程序或者nodejs程序
pcntl_signal是给一个进程安装信号处理器，比如说给当前进程安装一个“A信号”处理器，那么当当前进程收到A信号后，就会作出相应的反应。比如我们给当前进程安装一个usr1信号，然后自己就会重启。
pcntl_signal_dispatch，当给进程通过pcntl_signal安装了信号处理器后，还需要通过这个函数分发起来，不分发起来是完全没用的，进程收到信号并不会有什么反应。
pcntl_alarm，给一个进程设置闹钟信号，就是每隔x时间就给某进程发送一次SIGALRM信号，该进程收到后作出相应反应。
这些基本上就是常用的php多进程操作的主要函数。

进程和线程是有很大区别的。我们知道，进程是操作系统最小操作单元，进程之于操作系统，就好比线程之于进程。一个进程中可以含有多个线程。但是二者有很大的区别：
1 创建进程代价相对于创建线程是比较大的
2 进程间的切换的代价相对于线程间切换是比较大的
3 多个进程而言，每个进程都拥有自己的存储空间、堆栈、数据，彼此之间并不能共享；而线程是属于同一个进程的，说白了是没有自己隐私空间的，变量、数据、内存都是共享的，如果涉及到多个线程操作同一个变量，那么此时就一定要注意给变量加锁，这就是传说中的线程安全问题。而对于多进程而言，就不会有这个问题，因为完全相同的两个变量名，如果属于两个进程，那么两个进程操作这两个变量彼此之间不会有任何问题。当然了，这中优势你可以看作是劣势，而线程的这个劣势又随时可以转换为优势

#### 你在平时开发中对MVC有什么理解？Logic或者Service层呢？

#### PHP多线程，线程之前如何共享数据


### php mysql扩展中的connet和pconnet有什么区别

### php的gc流程是什么样的,unset后会马上回收内存吗? 假如有非常大的数组,可能会超过内厝最大限制,你会怎么优化?
