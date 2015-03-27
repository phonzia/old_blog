---
layout: post
title: "Thrift 基本语法"
id: 2
---

Thrift 基本语法
===============
1、基本类型
-----------
* bool 布尔类型，true或者false
* byte 字节类型，长度为8位，有符号
* i16  16位有符号整形
* i32  32位有符号整形
* i64  64位有符号整形
* double 64位浮点数
* string 一串编码未知的文本或二进制串
需要注意的是，没有无符号整形。这是兼容多种语言所要求的。

2、结构体
------------
一个Thrift struct定义了一个用于多语言的通用对象，基本上相当于面向对象语言中的一个类。一个结构体的例子如下：

    struct Example {
        1:i32 number=10,
        2:i64 bigNumber,
        3:double decimals,
        4:string name="thrifty"
    }
    
3、容器
--------------
Thrift支持三种容器：
* list<type> 列表容器，会被直接转化为STL的vector, JAVA的ArrayList和其他脚本语言的内置数组.
* set<type> 元素无序唯一的集合容器，会被转化为STL set，JAVA HashSet, Python set或内置PHP/Ruby的内置字典.
* map<type1, type2> 一个key唯一的key-value映射map，会被转化为STL map, Java HashMap, PHP 关联数组, 或Python/Ruby的字典.

容器的类型可以是任何有效的Thrift类型，包括其他容器和结构体.

4、异常
--------------
异常的语法和结构体基本相同，除了异常使用的关键字是exception而不是struct.
生成的对象会继承目标语言的异常基类，以便和任何指定语言的异常处理机制更好的整合.

5、服务
---------------
服务是基于Thrift类型定义的. 服务的定义在语义上大概相当于面向对象语言中定义一个接口或一个纯虚抽象类. Thrift编译器会生成实现该接口的全功能客户端和服务端桩代码.
服务定义类似如下：

    service <name> {
        <returntype> <name>(<arguments>)
        [throws (<exceptions>)]
        ...
    }
    
实例如下:

    service StringCache {
        void set(1:i32 key, 2:string value),
        string get(1:i32 key) throws (1:KeyNotFound knf),
        void delete(1:i32 key)
    }
    
6、枚举
--------------
可以像C/C++那样定义枚举类型，如：

    enum TweetType {
        TWEET,       
        RETWEET = 2, 
        DM = 0xa,  
        REPLY   
    }        
 
    struct Tweet {
        1: required i32 userId;
        2: required string userName;
        3: required string text;
        4: optional Location loc;
        5: optional TweetType tweetType = TweetType.TWEET
        6: optional string language = "english"
    }
    
说明：
* 编译器默认从0开始赋值
* 可以赋予某个常量某个整数
* 允许常量是十六进制整数
* 末尾没有逗号
* 给常量赋缺省值时，使用常量的全称

7、注释
----------------
Thrift支持Shell注释风格，也支持C/C++的单行和多行注释风格.

8、名空间
---------------
Thrift中的命名空间同C++中的namespace和java中的package类似，它们均提供了一种组织（隔离）代码的方式。因为每种语言均有自己的命名空间定义方式（如python中有module），thrift允许开发者针对特定语言定义namespace：

    namespace cpp com.example.project  // a
    namespace java com.example.project // b
说明：
a．  转化成

    namespace com { namespace example { namespace project {
b．  转换成

    package com.example.project

9、文件包含
----------------
Thrift允许thrift文件包含，用户需要使用thrift文件名作为前缀访问被包含的对象，如：

    #include "tweet.thrift"
    ...
    struct TweetSearchResult {
        1: list<tweet.Tweet> tweets;
    }
* 注意包含时的tweet前缀

10、常量
-----------------
Thrift允许用户定义常量，复杂的类型和结构体可使用JSON形式表示。

    const i32 INT_CONST = 1234;    // 分号是可选的
    const map<string,string> MAP_CONST = {"hello": "world", "goodnight": "moon"} // 复杂结构可以使用JSON表示
