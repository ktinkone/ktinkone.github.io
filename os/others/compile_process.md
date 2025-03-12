# Compile Process
## 1. Preprocessing
```
gcc -E hello.c -o hello.i  
```
预处理：扩展头文件，替换宏定义
## 2. Compilation
```
gcc -S hello.i -o hello.s
```
把C代码转换成汇编代码（词法，语法解析）
## 3. Assembly
···
gcc -c hello.s -i hello.o
```
把汇编代码转换成二进制文件（汇编到机器指令的翻译）
## 4. Link
```
gcc hello.o -o hello
```
把多个.o文件链接（静态或者动态）起来