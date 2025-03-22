# 多道程序与分时多任务
## 批处理系统的局限性
1. 资源浪费，用户程序串行执行，如果程序A等待IO，程序B此时并不能执行，浪费CPU资源

## 多道程序
内存中同时放置多个程序A，B，C，即使A没执行完，也可以执行B。
（就怕A程序一直不退出，那B，C永远不能执行）

## 分时多任务
将CPU的资源划分为等长的固定时间片，Time Slice，由时钟来触发中断，每次出发中断，进入内核，来重新调度任务的执行。避免某个任务一直不释放CPU资源。

----
Q1: 多道程序系统下，为什么在进程PCB中，要保留进程内核态的寄存器信息，和ch2不一样，每个进程的内核态寄存器信息不同吗？哪里不同？
A1:是的，每个进程的内核态寄存器信息不同，需要分开保存。考虑下面这个场景，A进程通过write进入内核，write之后发生了switch，B进程通过read进入内核，也发生了switch，此时切换会A进程，A进程的内核态应该是一个运行完write，等待返回用户态的状态，相应的，也应该切回这个上下文。为什么ch2不需要，因为ch2没发生switch，所有在内核态的操作一次同步做完了。

## app链接方式
多个app同时运行，且同时驻留在内存中，此时还没有虚拟内存。意味着不同的程序链接的时候，base addr要不一样，当前是怎么实现的?  
观察代码，在user目录的CMakeLists中，有指定ch3每个程序的entry地址！
```
f ("${CHAPTER}" MATCHES 3t?)
        if(${NAME} STREQUAL ch4_mmap0)
            break ()
        elseif (${NAME} STREQUAL ch3t_stride0 OR ${NAME} STREQUAL ch2b_exit)
            math(EXPR ENTRY "0x80400000" OUTPUT_FORMAT HEXADECIMAL)
        else ()
            math(EXPR ENTRY "${ENTRY} + 0x20000" OUTPUT_FORMAT HEXADECIMAL)
        endif ()
    endif ()
```

## 任务切换
有点反直觉，current->proc指向的是上次退出的进程，而不是中断陷入的进程。不过当前这两个一定是一个进程。

## 时钟中断
Q1: 如果发生时钟中断的时候，系统正在运行内核，正处在S态运行，不就出现嵌套中断了吗，咋办？
A1: 首先，当前os中， 时钟中断的处理是被委托到S mode进行处理的。当前还没有进行中断屏蔽设置，应该会出现嵌套的情况。但是当前os不支持嵌套中断。
    当os进入中断处理后，会先修改stvec寄存器的值,kerneltrap 会直接panic。
```
void kerneltrap()
{
	if ((r_sstatus() & SSTATUS_SPP) == 0)
		panic("kerneltrap: not from supervisor mode");
	panic("trap from kernel\n");
}

void set_kerneltrap(void)
{
	w_stvec((uint64)kerneltrap & ~0x3); // DIRECT
}
```

## chapter3 练习
实现sys_task_info系统调用

### 关注用户态到内核态的传参和内核态给用户态返回数据
1. syscall times： 这个好弄，加一下就可以了
2. state： 这个也好弄
3. time： 这个感觉有点细节要考虑好！
#### time
Stage1：记录某个进程从内核返回的时间戳和再次进入内核的时间戳，就是运行的时间。 
Q1： 运行过程中发生时间中断怎么办？上面逻辑还适用吗？  
A1： 使用的，时钟中断进入内核态，curr->proc还是sret之前的那个，符合上面的计算方式。  
Q2： 不对，进程在内核发生switch切换怎么办？那时间戳不就减错了吗？  
A1： 没错，计算只发生在ecall进入内核态这个时候，这个时候ecall前和后是同一个proc，但是需要proc执行sret的时候更新下时间戳！  
Q3: 话说proc在内核态的时间算是proc的运行时间吗  
A3：区分开来吧，进程的运行时间分为两类，用户态和内核态。
---
TMD，我说测试样例怎么一直不过，原来练习要求的是首次调度到调用get task info的间隔。
---

## 问答作业
1. 
2. 进入userret时， 
    1.a0执行trapframe的首地址，因为在usertrapret中这么调用userret： userret((uint64)trapframe);， a0 作为传参，传入的是trapframe的地址
    2. 删了，没影响，看着像是和虚拟内存相关，ch4再说吧
    3.因为现在a0指向的是trapframe，不是最早的用户传进来的a0, a0的值被swap到sccratch中了
    4. sret 指令返回用户态
    5. 执行完成后，a0 sscratch交换值，a0之前存的是用户态传参的参数，sscratch之前村的是trapframe的地址
    6. 从第5项开始保存，第五项之前的是proc的内核栈信息。内核栈信息是在proc从内核退出才要保存的，这里是要用这些信息恢复proc内核执行上下文
    7. 不是ecall吗？
    8. ld to, 16(a0), 语句的意思是加载内存位置a0+16的数据到t0,此时a0指向trapframe首地址，偏移16指向kernel_trap的地址，就是usrtrap()函数，然后jump过去执行