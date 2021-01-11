# GRPC

https://baijiahao.baidu.com/s?id=1633335936037018920&wfr=spider&for=pc

​	通过INT（In-band Network  Telemetry）技术可以实现流量端到端转发路径的可视化，如图1，但是无法对交换机的Buffer进行全面的管理，包括出、入端口/队列缓存等实时监控，显得有些无力，若是采用基于gRPC + Protocol Buffers的运维接口设计，可以很好地满足运维对单个网络网元全面的可视化和实时性要求。

​	gRPC是Google发布的基于HTTP 2.0传输层协议承载的高性能开源软件框架，提供了支持多种编程语言的、对网络设备进行配置和纳管的方法。

​	![img](https://pics3.baidu.com/feed/d439b6003af33a87a23f99fd505dc03c5243b589.jpeg?token=3c7fc45b9bcac3492c61ffc443814a5d&s=C26BB7529C78748A54476C54030080FE)

​	![img](https://pics2.baidu.com/feed/cdbf6c81800a19d8a9568081a5fb528fa71e46e6.jpeg?token=c3d762d34973f878787b6ad9e2e8f852&s=00A07D32191248CA0E5875DA0000E0B1)

- 交换机在开启gRPC功能后充当gRPC客户端的角色，采集服务器充当gRPC服务器角色；

- 交换机会根据订阅的事件构建对应数据的格式（GPB/JSON），通过Protocol Buffers进行编写proto文件，交换机与服务器建立gRPC通道，通过gRPC协议向服务器发送请求消息；

- 服务器收到请求消息后，服务器会通过Protocol Buffers解译proto文件，还原出最先定义好格式的数据结构，进行业务处理；

- 数据梳理完后，服务器需要使用Protocol Buffers重编译应答数据，通过gRPC协议向交换机发送应答消息；

- 交换机收到应答消息后，结束本次的gRPC交互。

  上图展示的是gRPC交互过程的具体流程，这也是Telemetry触发方式其中之一，称为Dial-out模式。简单地说，gRPC就是在客户端和服务器端开启gRPC功能后建立连接，将设备上配置的订阅数据推送给服务器端。我们可以看到整个过程是需要用到Protocol Buffers将所需要处理数据的结构化数据在proto文件中进行定义。



### 什么是Protocol Buffers?

可以理解Protocol Buffers是一种更加灵活、高效的数据格式，与XML、JSON类似，在一些高性能且对响应速度有要求的数据传输场景非常适用。

Protoco Buffers在gRPC的框架中主要有三个作用：

- 定义数据结构

  ![img](https://pics0.baidu.com/feed/0df431adcbef7609ff09f664b8dc73c87dd99ed5.jpeg?token=2de0fcb06ff79d693968efbb07ac3ec0&s=F2A4B5445AE1BF6E1CDCE4070000E0CB)

- 定义服务接口

  ![img](https://pics0.baidu.com/feed/4ec2d5628535e5dd3446df05e0c777ebcf1b628c.jpeg?token=0b0a33664439a97948e7c5f51801d109&s=FA85A144D3F1B960164C840E0100A0CB)

- 通过序列化和反序列化提高传输速率

我们知道使用XML、JSON进行数据编译时，数据文本格式更容易阅读，但进行数据交换时，设备就需要耗费大量的CPU在I/O动作上，自然会影响整个传输速率。Protocol Buffers不像前者，它会将字符串进行序列化后再进行传输，即二进制数据。