---
layout: post
title: " Lua C API 使用基础"
tag: [Lua]
---
# Lua C API 使用基础
#Lua
## 简单的解析器例子
参考《学习 Lua 程序设计》的例子，这里也用一个简单的解释器来开始学习 C API 的学习.
``` cpp
#include <iostream>
#include <lua.hpp>

int main() {
    std::string code_buff;
    int error = 0;
    lua_State* L = luaL_newstate(); // 新建 Lua 状态
    luaL_openlibs(L);               // 打开标准库

    std::cout << "> ";
    while(getline(std::cin, code_buff)) {
        error = luaL_loadbuffer(L, code_buff.c_str(), code_buff.length(), "line") || 
					lua_pcall(L, 0, 0, 0);
        if(error) {
            std::cerr << lua_tostring(L, -1) << std::endl; // 打印错误消息
            lua_pop(L, 1);								   // 从栈中弹出错误消息
        }
        std::cout << "> ";
    }

    lua_close(L);
    return 0;
}
```

头文件 `lua.hpp` 实际上是将使用 C API 所需的几个头文件都引入进来，内容如下：
``` cpp
// lua.hpp
// Lua header files for C++
// <<extern "C">> not supplied automatically because Lua also compiles as C++

extern "C" {
#include "lua.h"
#include "lualib.h"
#include "lauxlib.h"
}
```

其中 `lua.h`定义了 Lua 提供的基础函数；`lualib.h` 定义了标准库；`lauxlib.h`定义了辅助库。辅助库的所有函数定义都是以 `luaL_`开头的，比如上面例子用到的 `luaL_newstate`、`luaL_openlibs`，其中`luaL_openlibs`打开了所有的 Lua 标准库。
当创建好一个状态并打开标准库之后，便可以开始解析用户输入了。这里调用`luaL_loadbuffer`来编译用户的输入，如果编译没有错误，此调用会向栈中压入编译好的程序块。然后程序再调用`lua_pcall`执行程序块。这里`lua_pcall`表示在保护模式下运行，相应的非保护模式的调用时`lua_call`。保护模式下调用出错不会导致程序退出，而是将错误信息压入到栈中，通过`lua_tostring`可以获取这条消息。对消息进行处理之后，就可以用`lua_pop`将它从栈中删除。
## Lua 栈
Lua API 使用一个栈来进行 Lua 和 C 语言之间的数据交换。栈中的每个元素都能够保存任意类型的 Lua 值。要获取一个 Lua 值（比如一个全局变量），就必须调用一个 Lua API 函数，然后 Lua 会将获取到的值压入栈中，再通过调用从栈中取数据的 API 函数，将该值从栈中取出。当调用 Lua 时，Lua 只会改变栈的顶部，而 C/C++ 代码有更高的自由度，可以检索和改变栈任意位置的元素。
### 元素压入
为了将不同类型的数据压入栈中和取出，对应不同类型的压入函数，常用的压入函数有以下几个：
``` cpp
void lua_pushnil (lua_State *L);
void lua_pushboolean (lua_State *L, int bool);
void lua_pushnumber (lua_State *L, lua_Number n);
void lua_pushinteger (lua_State *L, lua_Integer n); 
void lua_pushunsigned (lua_State *L, lua_Unsigned n);
void lua_pushlstring (lua_State *L, const char *s, size_t len);
void lua_pushstring (lua_State *L, const char *s);
```

number一般大小和`double` 一致，integer大小一般和`ptrdiff_t`一致，unsigned大小则和`uint32_t`一致。
### 元素取出
对于栈索引，`1`为第一个被推入栈的元素，`2`为第二个，以此类推。也可以使用负数作为索引从栈顶开始访问，`-1`为栈顶，`-2`为之前一个入栈的元素，依次类推。

判断栈中元素类型可以使用以下一族函数：

``` cpp
int lua_is* (lua_State *L, int index);
```

从栈上获取元素的值，可以用以下一组函数：
``` cpp
int lua_toboolean (lua_State *L, int index); 
const char *lua_tolstring (lua_State *L, int index, size_t *len); 
lua_Number lua_tonumber (lua_State *L, int index); 
lua_Integer lua_tointeger (lua_State *L, int index); 
lua_Unsigned lua_tounsigned (lua_State *L, int idx);
```

一般来说类型错误会返回`NULL`或者`0`，我们一般都在获取之前判断类型是否正确。但 Lua5.2 引入了下列的新函数，通过 `isnum` 返回类型是否能被转换为数字。
``` cpp
lua_Number lua_tonumberx (lua_State *L, int idx, int *isnum); 
lua_Integer lua_tointegerx (lua_State *L, int idx, int *isnum); 
lua_Unsigned lua_tounsignedx (lua_State *L, int idx, int *isnum);
```

`lua_tolstring` 返回的指针指向的内容是不能更改的，Lua 会保证栈存在的时候指针是有效的。当 C 函数返回时，Lua会清空它的栈，所以不要在函数之外直接存这些返回的指针。注意`lua_tolstring` 返回的指针指向的空间最后总会在最后有一个`\0`，但是由于中间还可能有其他0，所以必须通过`len`来确认返回指针指向数据的真实长度。
### 其他栈操作
这里简单列出几个一般的栈操作函数：
``` cpp
int lua_gettop (lua_State *L); // 获取栈中元素个数
void lua_settop (lua_State *L, int index); // 设置栈顶位置
void lua_pushvalue (lua_State *L, int index); // 将指定索引位置元素的副本推入栈中
void lua_remove (lua_State *L, int index); // 移除指定索引位置的元素
void lua_insert (lua_State *L, int index); // 在指定位置增加一个元素位，将之上的元素上移
void lua_replace (lua_State *L, int index); // 弹出栈顶值，并设置到指定索引
```
## 从 Lua 中调用 C
### C 函数编写
每一个 提供给 Lua 调用的 C 函数都必须具有以下函数类型：
```cpp
typedef int (*lua_CFunction) (lua_State *L);
```
该 C 函数只有一个参数，就是 Lua 的状态。返回值是一个整数，代表着该函数压入栈的返回值的数量，即 Lua 调用该函数获得的返回值的数量。这里引用一个《学习 Lua 程序设计》中的例子来讲解：
```cpp
static int l_sin(lua_State *L) {
    double d = luaL_checknumber(L, 1); // 1栈中的第一个元素，也对应着函数的第一个参数
    lua_pushnumber(L, sin(d)); // 压入一个返回值
    return 1; // 返回返回值个数
}
```
完成 C 函数的编写之后，需要将函数注册到 Lua 状态中：
``` cpp
lua_pushcfunction(L, lsin);
lua_setglobal(L, "mysin");
```
这样，就可以在 Lua 代码中调用`mysin`函数
``` lua
mysin(30)
```
### 编写 C 函数的技术
#### table 操作
##### 获取 table 中的值
假设我们的table在栈顶，如果要取table中某个key的值，则需要以下代码：
``` cpp
lua_pushstring(L, key); //将key入栈，如果key是数字，用lua_pushnumber等也可以
lua_gettable(L, -2); //获取key对应的value值并放到栈顶。此处-2是table的索引，因为key现在是栈顶
```
也可以用以下代码：
``` cpp
lua_getfield(L, -1, key); // 这里-1是table的索引，table在栈顶，如果table不在栈顶，则此处应填table的索引位置。
```
现在value值在栈顶，用 `lua_to*` 一族函数就可以取出值，注意取出值之后要使用`lua_pop(L,1)`删除保存的值，以确保不会栈溢出。
##### 设置 table 中的值
假设 table 在栈顶，则以下代码可以设置元素的值：
``` cpp
lua_pushstring(L, key); // 将 key 入栈
lua_pushnumber(L, value); // 将 value 入栈
lua_settable(L, -3); // 假设一开始 table 在栈顶，则入栈 key, value 之后，table 在 -3 的位置，也可以直接指定 table 的位置
```
也可以使用以下代码：
``` cpp
lua_pushnumber(L, value); // 将 value 入栈
lua_setfield(L, -2, key); // 假设一开始 table 在栈顶，则入栈value 之后，table 在 -2 的位置，也可以直接指定 table 的位置
```
#### 数组操作
数组在 Lua 中只是 table 的一种特殊的使用方式，但是 Lua API 中处于性能和使用方便考虑，提供了一组专门的函数：
``` cpp
void lua_rawgeti(lua_State *L, int index, int key);
void lua_rawseti(lua_State *L, int index, int key);
```
#### 获取数组中的值
假设 table 在栈顶，取数组中的值：
``` cpp
lua_rawgeti(L, -1, key); // 执行之后值被放置于栈顶
```
现在value值在栈顶，用 `lua_to*` 一族函数就可以取出值，注意取出值之后要使用`lua_pop(L,1)`删除保存的值，以确保不会栈溢出。
#### 设置数组的值
假设 table 在栈顶，设置数组的值:
``` cpp
lua_pushstring(L, value); // 将 value 入栈
lua_rawseti(L, -2, key); // 入栈 value 之后，table 在 -2 的位置
```
