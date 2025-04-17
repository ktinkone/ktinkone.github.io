# Cross Compiler
# why
1. host gcc complie program under the assume that this porgram will run on the host enviroment
2. host environment is not the same as target environment where our os will running on
3. what we need is a pure binary file which does not rely on any lib or os and the target instruction should be the same as target architecture
## What Compiler needs to know
### Target Triplet
1. Target Arch (x86-32)
2. Target manufacturer --> (why need this?)
2. Target Operating System (none)
---
---
---
# Build a Cross-Compiler
## Host Compiler Env
1. gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0
## Traget Cross-Compiler Env
1. Gcc 11.3.0
2. Binutils: 2.38
3. libc: newlib 4.3.0
```
Q: What is the difference between gcc and binutils? Does binutils contain gcc?
A: binutils and gcc is two indival project, binutils contain ar, ld , objdump and so on. when compile programs, gcc translate c file to assemble file and it use binutils to convert assemble file to binary file. Different gcc can work with different binutils.
```
## Building GCC
参照osdev上的步骤，略
### 遇到的问题
```
编译libstdc++-v3的时候，遇到报错：
--> checking for shl_load... configure: error: Link tests are not allowed after GCC_NO_EXECUTABLES.

解决方法： 问题是在编译libstdc++-v3的时候遇到的，询问deepseek，stdc++的编译依赖libc的文件，再交叉编译stdc++时，需要先交叉编译libc
解决步骤：
        1. 下载newlib，并进行交叉编译
        2. 编译stdc++
```

## 总结
时间主要还是花在解决libstdc++编译报错上
网上搜了相应报错，很多都是提出问题，但是没有具体的解决方案
每次到这一步的时候，就会很迷茫，不知道该咋办了，看源码？还是继续搜？
最后还是AI帮忙解决了。

---

Q1: libc和libstdc++的区别  
A1: 这两个lib都是用户态进程需要去链接的，理论上os是不需要链接的。lib里面实现的都是一些系统调用的封装。这些正是os要求实现的  
Q2: 为什么gcc里面包含libstdc++,但是不包含libc?  
A2: c++标注库需要编译器深度配合，基本每个gcc版本都有对应的libstdc++。 libc比较稳定，且独自维护  

---
