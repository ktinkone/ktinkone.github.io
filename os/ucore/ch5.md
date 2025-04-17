# 进程及进程管理

```
Q0: 为什么运行ch5b_usertest，进程名全是NULL
A1: 
    是不是数据段没加载？

    Stage 1: 
        看看app在内存中的布局，对应的test数组是不是空的？
        不是
    
    Stage 2：
        为啥用户态访问的时候，是空的？

        Stage 2.1: 
            gdb 调试用户态程序
            无法打用户态程序的断点，怎么办？ ---> 指令级别调试
            程序总是跑飞，是真TM难搞
        
        Stage 2.2:  （date 4.6）
            debug 调试
            怀疑TESTS加载的时候没映射好，添加打印，看映射和拷贝的内存块是否正确

            发现程序装载进内存的时候，TESTS那块地址对应的内存值是全0
        
        Stage 2.3:   （date 4.13）
            从ELF文件里面看，TESTS被放在了.data段，但是相应地址的值全是0
            奇怪，为什么.data段的值是0呢？.data段不应该是指针的值吗？

        Stage 2.4:    （date 4.17）
            看看Makefile中完整的编译链接流程吧，看看链接前后是咋样的！

            发现有一个地方很诡异： CMake文件中指定的交叉编译工具的PREFIX有点奇怪，指定的是-linu-musl，不应该是-unknown-elf吗
            （换成-unknown-elf后，就可以了）
            （为啥之前没问题？）

Q1: ch5中init进程和shell进程是同一个进程吗，当前内核拉起的第一个进程是什么进程？

Q2: 当前的OS支持时间中断，是不是意味着usershell程序会被调度走，shell终端无响应？

Q3: 当前usershell支不支持后台运行？即&，如果不支持，同步等待又是怎么实现的（即在usershell拉起的程序执行结束前，usershell都不执行？）？
```
