## 1、gdb是什么？用过么
显然我没用过...

经过一顿百度后

GDB是一个Linux常用的调试工具（没有图形化界面的23333）基本上和在IDE里调试区别不大（我猜的）  
1、启动你的程序，可以按照你的自定义的要求随心所欲的运行程序。  
2、可让被调试的程序在你所指定的调置的断点处停住。（断点可以是条件表达式）  
3、当程序被停住时，可以检查此时你的程序中所发生的事。  
4、动态的改变你程序的执行环境。

暂时先写这写，之后用到了再来补充

## 2、生产者消费者模型
这个模型的核心是通过一个容器来解决生产者消费者之间强耦合的问题。生产者和消费者之间并不直接通信，而是通过一个阻塞队列来完成交互，所以生产者生产完数据之后不用等待消费者处理，直接扔给阻塞队列，消费者不找生产者要数据，而是直接从阻塞队列里取，阻塞队列就相当于一个缓冲区，平衡了生产者和消费者的处理能力。  
这个模型降低了生产者消费者之间的耦合度，提供了对并发的支持，还解决的忙闲不均的问题。

## 3、进程间通信方式
这题我会(o゜▽゜)o
### 3.1管道
管道是一种半双工的通信方式，数据只能单向流动。其只能用于有亲缘关系的进程之间。  
原理：管道实为内核使用环形队列机制（以便管道可以重复利用），借助内核缓冲区（4k）实现  
本质：是一个伪文件（实为内核缓冲区），由两个文件描述符引用，一个表示读端，一个表示写端。数据从管道的写端流入，从读端流出。  
局限性：  
数据不可反复读取，数据被读走后就不存在了  
采用半双工，数据无法双向流动
### 3.2命名管道
有名管道（命名管道）可以让不相关（没有血缘关系）的进程也能交换数据。  
原理：FIFO是Linux基础文件类型中的一种，但是FIFO文件在磁盘上没有数据块，只是用来标识内核中一条通道。进程可以打开这个文件进行read和write操作，实际上是在读写内核通道，进程只要能够访问到该路径，则能实现进程间通信。
### 3.3消息队列
消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。  
消息队列中间件是分布式系统中重要的组件，主要解决应用解耦，异步消息，流量削锋等问题，实现高性能，高可用，可伸缩和最终一致性架构。目前使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ。  
消息队列使利用发布-订阅模式工作，消息发送者（生产者）发布消息，一个或多个消息接受者（消费者）订阅消息。对新增业务，只要对该类消息感兴趣，即可订阅该消息，对原有系统和业务没有任何影响，从而实现网站业务的可扩展性设计。
### 3.4共享存储
共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。
### 3.5套接字
套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信
### 3.6信号量
就是Semaphore，操作系统学过的那个  
信号量是一个计数器，用来控制多个进程对资源的访问，其通常作为锁机制，防止进程之间访问统一资源造成冲突
### 3.7信号
单个的信号，很少见。

## 4、TCP/IP的七层协议
### 4.1物理层
主要是定义一些物理上的内容。连接头、帧、帧的使用、电流、编码及光调制等都属于各种物理层规范中的内容。物理层常用多个规范完成对所有细节的定义
### 4.2数据链路层
定义了单个链路上数据如何传输，其协议与介质有关
### 4.3网络层
对端到端的包传输进行了定义，还定义了逻辑地址用以标识节点和路由的实现及学习方式。此外为了适应传输不了一整个包的介质，还定义了将包分段的方法
### 4.4传输层
这层的功能包括是否选择差错恢复协议还是无差错恢复协议，及在同一主机上对不同应用的数据流的输入进行复用，还包括对收到的顺序不对的数据包的重新排序功能
### 4.5会话层
它定义了如何开始、控制和结束一个会话，包括对多个双向消息的控制和管理，以便在只完成连续消息的一部分时可以通知应用，从而使表示层看到的数据是连续的，在某些情况下，如果表示层收到了所有的数据，则用数据代表表示层
### 4.6表示层
这一层的主要功能是定义数据格式及加密。例如，FTP允许你选择以二进制或ASCII格式传输。如果选择二进制，那么发送方和接收方不改变文件的内容。如果选择ASCII格式，发送方将把文本从发送方的字符集转换成标准的ASCII后发送数据。在接收方将标准的ASCII转换成接收方计算机的字符集
### 4.7应用层
与其它计算机进行通讯的一个应用，它是对应应用程序的通信服务的。例如，一个没有通信功能的字处理程序就不能执行通信的代码，从事字处理工作的程序员也不关心OSI的第7层。但是，如果添加了一个传输文件的选项，那么字处理器的程序就需要实现OSI的第7层

## 5、内存对齐
### 5.1数据成员
结构(struct)(或联合(union))的数据成员，第一个数据成员放在offset为0的地方，以后每个数据成员存储的起始位置要从该成员大小或者成员的子成员大小（只要该成员有子成员，比如说是数组，结构体等）的整数倍开始(比如int在３２位机为４字节, 则要从４的整数倍地址开始存储),基本类型不包括struct/class/uinon
### 5.2结构体成员
如果一个结构里有某些结构体成员,则结构体成员要从其内部"最宽基本类型成员"的整数倍地址开始存储.(struct a里存有struct b,b里有char,int ,double等元素,那b应该从8的整数倍开始存储.)
### 5.3补充
结构体的总大小,也就是sizeof的结果,.必须是其内部最大成员的"最宽基本类型成员"的整数倍.不足的要补齐.

## 6、静态库和动态库的区别
静态库和动态库最大的区别就是在编译时库是否被编译到了程序的内部
### 6.1静态库
一般扩展名为（.a或.lib）,这类的函数库通常扩展名为libxxx.a或xxx.lib
静态库在编译时会直接将库整合进程序中（这会让程序比较大），优点是编译成的可执行文件可以单独运行，不再需要外部库的支持。但是这使得其每次更新函数库都需要重新编译，会增大升级难度。
### 6.2动态库
动态函数库的扩展名一般为（.so或.dll），这类函数库通常名为libxxx.so或xxx.dll
动态库在编译时，程序中只有一个“指向”的位置，只有当可执行文件需要使用时，程序才会去读取库函数来使用。这种方法会使得可执行文件无法单独运行，但是在升级时也只需要更新动态库即可，而无需重新编译可执行文件。

## 7、Linux的内核态
内核从本质上看是一种软件——控制计算机的硬件资源，并提供上层应用程序运行的环境。用户态即上层应用程序的活动空间，应用程序的执行必须依托于内核提供的资源，包括CPU资源、存储资源、I/O资源等。为了使上层应用能够访问到这些资源，内核必须为上层应用提供访问的接口：即系统调用。不过一般来说会用库函数和shell。

## 8、Linux用户态怎么进入内核态
有三种方式
### 8.1系统调用
系统调用时操作系统的最小功能单位。根据不同的应用场景，不同的Linux发行版本提供的系统调用数量也不尽相同，大致在240-350之间。这些系统调用组成了用户态跟内核态交互的基本接口。
### 8.2异常
比较典型的就是缺页异常。
### 8.3中断
外设完成用户请求时会向CPU发送中断信号。

## 9、C++中static关键字的作用
在不同情况下有不同作用  
虽然不建议在头文件中使用static，但写进去并不会报错，如果两个文件都调用了这个头文件中的static int x，则每个文件会有自己单独拷贝一个x。  
### 9.1静态全局变量
在全局变量前加static，该变量就会被定义为全局静态变量。
#### 特点：  
该变量将会在全局/静态数据区分配内存  
若未被初始化，该变量会自动初始化为0
静态全局变量仅在声明他的文件中可见
### 9.2静态局部变量
在局部变量前，加上关键字static，该变量就被定义成为一个静态局部变量。
局部静态变量保存在全局函数区（普通的局部变量会在堆里面），这样的好处是不会随着函数体调用结束而被收回，可以留到下次调用，递归中可以用到。
#### 特点：
该变量将会在全局/静态数据区分配内存  
静态局部变量在程序执行到该对象的声明处时被首次初始化，即以后的函数调用不再进行初始化  
若未被初始化，该变量会自动初始化为0  
它始终驻留在全局数据区，直到程序运行结束。但其作用域为局部作用域，当定义它的函数或语句块结束时，其作用域随之结束
### 9.3静态函数
在函数的返回类型前加上static关键字,函数即被定义为静态函数。静态函数与普通函数不同，它只能在声明它的文件当中可见，不能被其它文件使用
#### 特点：
不能被其他文件调用
其他文件可以定义相同名字的函数  
### 9.4类中的静态数据成员
类中的数据成员前加上static可定义一个静态数据成员
#### 特点：
无论这个类的对象被定义了多少个，静态数据成员在程序中也只有一份拷贝，由该类型的所有对象共享访问。也就是说，静态数据成员是该类的所有对象所共有的。对该类的多个对象来说，静态数据成员只分配一次内存，供所有对象共用。所以，静态数据成员的值对每个对象都是一样的，它的值可以被任何一个对象更新。
静态数据成员定义时要分配空间，所以不能在类声明中定义
### 9.5类中的静态成员函数
类中的函数前加static可以定义一个静态成员函数，静态成员函数与静态数据成员一样，都是类的内部实现，属于类定义的一部分
只有类中的函数才能定义为静态
#### 特点：
它为类的全部服务而不是为某一个类的具体对象服务
静态成员之间可相互访问，包括静态成员函数访问静态数据成员和访问静态成员函数
非静态成员可以任意访问静态成员
静态成员不能访问非静态函数和成员
静态函数没有this

## 10、lambda表达式
c++11新增加的特性之一，lambda表达式又称为匿名函数，是许多编程语言都支持的概念，有函数体，没有函数名。

lambda表达式的基本格式如下： 

    [capture](parameters)->return-type {body}  
    
    []叫做捕获说明符，表示一个lambda表达式的开始。接下来是参数列表，即这个匿名的lambda函数的参数,  
    ->return-type表示返回类型，如果没有返回类型，则可以省略这部分
    
    举一个例子：
    sort(v.begin(),v.end(),[](int a,int b){
        return a<b;
    });
    这个是对vector进行排序，排序方法是用lambda表达式写的

在平时使用中还没有遇到出了sort之外很有必要用的地方，后面再遇到会继续补充

## 11、左值引用和右值引用
左值是可以放在赋值号左边可以被赋值的值；左值必须要在内存中有实体；  
右值当在赋值号右边取出值赋给其他变量的值；右值表示无法获取地址的对象，有常量值、函数返回值、Lambda表达式等，右值可以在内存也可以在CPU寄存器。  
一个对象被用作右值时，使用的是它的内容(值)，被当作左值时，使用的是它的地址。  
  
一般来说正常的引用是给一个变量取的一个别名，因此普通的引用必然是引用的左值（因为变量必然是左值） 
  
右值引用一般用于移动语义和完美转发  
  
移动语义中不创建新的变量的方法就是进行右值引用

完美转发是指函数模板可以转缺的转发参数的值，不改变其左值右值的属性

## 12、c++模板
模板是泛型编程的基础，泛型编程即以一种独立于任何特定类型的方式编写代码。

### 函数模板：
    写法大概是这样
    template <typename T>
    inline T const& Max (T const& a, T const& b) 
    { 
        return a < b ? b:a; 
    } 
    T感觉类似于auto?
    
    测试了一下这样可以：
    inline auto const& Max (T const& a, T const& b)
    {
        return a < b ? b:a;
    }
    但这样不行：
    inline auto const& Max (auto const& a, auto const& b)
    {
        return a < b ? b:a;
    }
### 类模板
    这么弄
    template <class T>
    class Stack { 
    private: 
        vector<T> elems;     // 元素 
    public: 
        void push(T const&);  // 入栈
        void pop();               // 出栈
        T top() const;            // 返回栈顶元素
        bool empty() const{       // 如果为空则返回真。
            return elems.empty(); 
        } 
    }; 
 
    template <class T>
    void Stack<T>::push (T const& elem) 
    { 
        // 追加传入元素的副本
        elems.push_back(elem);    
    } 
