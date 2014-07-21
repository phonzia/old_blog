---
layout: post
title: "Boost thread的使用--线程的创建"
---

boost::thread是boost的线程库，并且c++11的新线程库基本上也是参照boost::thread实现。本文是对boost::thread使用的简述。

线程的创建
==================

## 1、 使用函数指针的线程创建 ##

	//boost_thread_hello.cpp
	#include <boost/thread/thread.hpp>
	#include <iostream>

	void hello() {
	  std::cout << "Hello world, I'm a thread!"<< std::endl;
	}

	int main(int argc, char* argv[]) {
      boost::thread thrd(&hello);
      thrd.join();
      return 0;
	}

按以下命令编译：

	g++ boost_thread_hello.cpp -lboost_thread -lboost_system -o boost_thread_hello

执行结果：

	Hello world, I'm a thread!

## 2、 使用函数对象的线程创建 ##
函数对象是通过重载()运算符，来使得类对象行为类似函数。一个简单的函数对象的定义和使用如下：

	class FuncObj {
	  public:
	    void hello() {
	      std::cout << "Hello world, I'm a thread!"<< std::endl;
	    }
	};

    int main(int argc, char* argv[]) {
      FuncObj hello;
	  hello();
      return 0;
	}

使用函数对象作为工作函数的boost::thread使用如下：

	int main(int argc, char* argv[]) {
	  FuncObj hello;
      boost::thread thrd(hello);
      thrd.join();
      return 0;
	}

由于类对象可以带有成员，所以函数对象可以带有一些初始化数据，相比函数指针，更加灵活一些。

## 3、 使用boost::bind的线程创建 ##
boost::bind可以将函数和指定的参数绑定，并返回一个boost::function对象，相比普通自定义的函数对象，使用更加方便。具体的代码示例如下：

	//boost_thread_hello.cpp
	#include <boost/thread/thread.hpp>
	#include <iostream>

	void hello(const std::string& str) {
	  std::cout << str << std::endl;
	}

	int main(int argc, char* argv[]) {
      boost::thread thrd(boost::bind(hello, "Hello world, I'm a thread!"));
      thrd.join();
      return 0;
	}