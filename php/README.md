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

#### 你在平时开发中对MVC有什么理解？Logic或者Service层呢？

#### PHP多线程，线程之前如何共享数据


### php mysql扩展中的connet和pconnet有什么区别

### php的gc流程是什么样的,unset后会马上回收内存吗? 假如有非常大的数组,可能会超过内厝最大限制,你会怎么优化?
