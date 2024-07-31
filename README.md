# webServer2
C++11标准的简易高性能webServer


## 简介
* 本项目使用C++11标准编写了一个遵循One Loop Per Thread思想的Web高性能服务器。
* 并发模型使用主从Reactor模式+线程池，Socket使用非阻塞IO，IO多路复用使用Epoll ET边缘触发工作模式。
* 使用状态机解析HTTP请求，支持HTTP GET、POST、HEAD请求方法，支持HTTP长连接与短连接。
* 使用小根堆做定时器，惰性删除超时的任务，即客户端没有向服务器发送请求的时间已经超过了我们给定的超时时间，当再次访问它的时候才会去关闭这个连接。
* 实现了双缓冲区的异步日志系统，记录服务器运行状态。
* 使用智能指针等RAII机制，减少内存泄漏的可能。

## 环境 
* OS: Ubuntu 22.04
* Complier: g++ 11.4.0
* Debugger: gdb 12.1
* CMake: 3.22.1
* Makefile: 4.3

## 构建
* 使用CMake来build

    cmake -S . -B build
    make -j8 build&&make build install
    可执行文件在bin目录下

* 使用Makefile来build
    
    make -j8 && make install
    可执行文件在bin目录下

    make clean清空

## 运行
	./web_server [-p port] [-t thread_numbers] [-f log_file_name] [-o open_log] 
    [-s log_to_stderr] [-c color_log_to_stderr] [-l min_log_level]

    或者
    ./run_server.sh

## 压力测试
* 本项目对开源压测工具WebBench

## 压测结果
* 压测之前做的工作：
设置了几个关于操作系统对单个进程资源的一些限制, 用limit命令查看, 
首先为了方便调试程序(程序段错误core dump), 将codedumpsize设置为unlimited, 
然后将单个进程可以打开的文件描述符descriptors设置成了100w, 
最后设置了能够使用的端口号，设置的从10000开始到65536都可以使用，也就是5w多个端口。

* 在程序层面，因为压测的是echo server, 响应报文除了状态行和响应头，响应体部分是简单的hello world, 
所有程序中关闭了套接字的TCP Nagle算法, 避免响应时间过久，每次数据直接发，而不用等到一定量再一起发。

* 压力测试开启1000个进程，访问服务器60s，过程是客户端发出请求，然后服务器读取并解析，返回响应报文，客户端读取。
长连接因为不必频繁的创建新套接字去请求，然后发送数据读取数据，关闭套接字等操作，所以比短连接QPS高很多。

HTTP短连接 QPS: 26万
   WebServer2/resource/WebServer短连接QPS.png
