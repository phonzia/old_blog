---
layout: post
title: "Thrift在Cpp中的使用"
---

Thrift在Cpp中的使用
===================
1、定义简单的服务
-------------------
定义一个简单的thrift文件如下：

    //hello.thrift
    namespace cpp test
    namespace java test
    namespace php test
    namespace perl test

    service Hello {
        string getKey(),
        string getValue()
    }
 
上面的文件提供了一个Hello的服务，拥有两个函数接口getKey和getValue。

2、生成代码
---------------------
执行以下命令以生成代码：

    thrift --gen cpp hello.thrift
会在当前目录下生成gen-cpp的文件夹，其中文件如下：、

    Hello.cpp  Hello.h  Hello_server.skeleton.cpp  test_constants.cpp  test_constants.h  test_types.cpp  test_types.h
这里主要需要关注的是Hello_server.skeleton.cpp, 其中给出了一个简单的thrift server的框架。

{% highlight cpp %}
    class HelloHandler : virtual public HelloIf {
     public:
      HelloHandler() {
        // Your initialization goes here
      }
    
      void getKey(std::string& _return) {
        // Your implementation goes here
        printf("getKey\n");
      }

      void getValue(std::string& _return) {
        // Your implementation goes here
        printf("getValue\n");
      }

    };

    int main(int argc, char **argv) {
      int port = 9090;
      shared_ptr<HelloHandler> handler(new HelloHandler());
      shared_ptr<TProcessor> processor(new HelloProcessor(handler));
      shared_ptr<TServerTransport> serverTransport(new TServerSocket(port));
      shared_ptr<TTransportFactory> transportFactory(new TBufferedTransportFactory());
      shared_ptr<TProtocolFactory> protocolFactory(new TBinaryProtocolFactory());

      TSimpleServer server(processor, serverTransport, transportFactory, protocolFactory);
      server.serve();
      return 0;
    }
{% endhighlight %}
    
这里需要做的，是补全HelloHandler。然后就可以通过简单的客户端来访问。这里给出简单的客户端代码:

    {% highlight cpp %}
    int main(int argc, char** argv) {
      shared_ptr<TTransport> socket(new TSocket("localhost", 9090));
      shared_ptr<TTransport> transport(new TBufferTransport(socket));
      shared_ptr<TProtocol> protocol(new TBinaryProtocol(transport));
      HelloClient client(protocol);

      try {
        transport->open();
        std::string str;
        client.getKey(str);
        std::string value;
        client.getValue(value);
        std::cout << str << value << std::endl;
      } catch (TException &tx) {
        printf("ERROR: %s\n", tx.what());
      }
    }
{% endhighlight %}

需要注意的是，如果不是如生成的简单server框架一样使用TSimpleServer，而是使用非阻塞的TNonblockingServer，那么相应的客户端transport必须使用TFramedTransport而不是TBufferTransport。
