## Linux

### 1 基础
* Linux系统 = 用户空间 + 内核空间

* “Unix下一切皆文件”，如键盘、鼠标、网卡、标准输出等

* Inode：索引节点，文件的唯一标识，存储文件数据的信息，如文件大小、属主、归属用户组、读写权限、、

* Block：块，存储文件的数据

* 硬链接：一个Inode对应多个文件名，即多个文件名指向同一个文件，如 ln hello.c hello2.c

* 软链接：ln -s ，目录只能创建软链接，不能创建硬链接，避免形成环形文件系统  

* 文件描述符：是一个整型的数据，所有对文件的操作都通过文件描述符实现
   1. 连接用户空间和内核空间的枢纽
   2. 创建文件：内核空间 --> 创建相应结构，生成整型文件描述符 -->传递给用户空间
   3. 读写文件：用户空间 -->文件描述符-->read/write -->内核对对应文件运行相应函数--> 返回结果

* socket文件：Linux中通过网络进行通信的方式，通过文件描述符抽象实现，访问网络和访问普通文件相识

----
### 2 进程/线程
##### 2.1 进程
1. 操作系统分配 内存、CPU时间片 等资源的基本单位
2. 产生：复制一份父进程的资源给子进程
   * 进程复制fork(): fork一次，返回2次，父进程返回子进程id，子进程返回0，创建失败返回-1
   * system(): 调用shell外部命令，在当前进程中开始另一个进程，阻塞当前进程直到command命令执行完毕
   * exec(): 用新进程替代原来进程，系统从新的进程运行，新的pid等于原来的pid
3. 终止：释放资源，如内存、文件符、内核结构等
4. IPC：进程间通信机制
4. 通信： 1.管道  2.共享内存
     1. 管道： 
        * int *read_fd &fd[0]
        * int *write_fd &fd[1]
        * result = pipe(fd)
        * 子进程： close(*read_fd)  result = write(*write_fd, string, strlen(string))
        * 父进程： close(*write_fd)  result = read(*read_fd, readbuffer, sizeof(readbuffer))
     2. 共享内存：
        * 对内存段进行映射
        * 创建
        * 获取
        * 删除
        * 控制
5. 同步：
     * 信号量：是一种计数器，控制多进程对共享资源的访问
        * 创建
        * 获取
        * P/+1
        * V/-1
        * 控制
        * 销毁
##### 2.2 线程
1. CPU调度的基本单位，较传统进程，系统资源消耗低，速度快，数据共享更容易
2. 基本操作：
   1. 创建： pthread_create()
   2. 结束： pthread_join()
3. 属性：优先级（了解一下）
4. 线程间互斥/mutex ：同一时间只有一个线程在执行（涉及多线程访问共享资源时）
   1. 初始化：pthread_mutex _init()
   2. 预锁定：pthread_mutex _trylock()
   3. 锁定：pthread_mutex _lock()
   4. 解锁：pthread_mutex _unlock()
   5. 销毁：pthread_mutex _destroy()
5. 信号量
   1. 初始化
   2. +1
   3. -1
   4. 销毁


### 3 网络编程
##### part1

      应用层        ---->       HTTP FTP SNMP
        |      
        |
      表示层        ---->       ASCII JPEG MPEG
        |
        |
      会话层        ---->       TCP UDP 管理会话过程(建立、终止)
        |
        |
      传输层        ---->       TCP/字节流 UDP/数据报  数据段
        |
        |
      网络层        ---->       IP 数据包packet 路由选择(路由器)
        |
        |   两层之间 ---->       ARP:地址解析 / IP地址<-->局域网地址
        |                       ICMP：网际控制报文协议/报告网络错误
        |
      数据链路层     ---->       封装成帧，流量控制，检测重发，物理寻址
        |                       区别：除头部外还有尾部，CRC16方式/校验和
        |
        |
      物理层        ---->       物理设备(网卡)  比特(bit)
                                              1G = 1024KB  1KB = 1024byte
                                              1byte = 8bit 1字 = 2byte
                                                     
##### part2

      1  TCP报文 = 源端口号 + 目的端口号 + 序列号Seq + 确认号Ack + TCP校验和 + 窗口尺寸/可接收的字节数

      2  UDP数据 = 源端口号 + 目的端口号 + UDP数据长度 + UDP校验和/CRC16校验和 + 数据
      
      3  IP头部 = 版本号 + TTL生存时间 + 协议类型TCP/UDP + 16位校验和/循环冗余校验法 + 源ip地址 +  目的ip地址
      
      4  ICMP： IP数据报出错后，对应路由器发送ICMP报文通知源主机（最多的是报文不可达的差错报文）
 
      5  IP地址分类： A/0/0~127   B/10/128~191  C/110/192~223  D/1110/224~239  E/1111/240~255
         IP地址32位 全1为广播地址，全0为某个网络的网络地址
   
      6  子网掩码：通过01按位与，确定ip地址的网段

      7  小端字节序：内存的低字节位存放数据低字节位，高-->高
         大端字节序：高-->低，低-->高
         主机字节序：可大可小
         网络字节序：大端字节序

##### part3

      HTTP：客户端 + 服务器
      
      FTP： 文件传输
 
      TELNET： 远程登录，QQ的远程控制请求方的电脑
