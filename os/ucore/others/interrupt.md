# riscv interrupt
riscv 中断分类
1. 同步中断（Exception）： 就是之前接触的trap和fault，由本CPU正在执行的指令触发
2. 异步中断（Interrupt）： 由本CPU外部因素或定时器触发，和本CPU正在执行的指令无关

## 异步中断
异步中断主要有如下分类
1. 软件中断（Software Interrupt）：处理核间通信（注意，这不是由本CPU正在运行的软件产生，对于本CPU来说，仍然是异步中断，通常情况下是另一个CPU的软件设置msip，导致本CPU触发中断）
2. 定时器中断（Timer Interrupt）： 定时器中断，比较特殊的一种外设中断，riscv每个CPU内部必须集成一个定时器
3. 外部中断（External Interrupt）： 就是一般理解的那个外设中断。

## 中断处理
Q1: 假如在U mode下触发一个外部中断，是切换到M mode运行还是S mode运行？
A1: riscv cpu默认情况下，所有的中断都进入M mode处理。但是riscv支持中断委托（通过设置中断委托寄存器 meddeleg、mideleg），将中断委托到S mode处理。U mode无论如何是没权限处理中断的。  
    一旦设置好中断委托后，后续的中断到来，中断的委托过程由硬件自动完成。

## 中断相关寄存器