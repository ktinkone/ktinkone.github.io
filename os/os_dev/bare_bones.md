# Booting the Operating System

## 遇到的问题
1. qemu-system-i386 -cdrom myos.iso 报错，找不到可运行的cdrom
```
根因是没有下载grub-pc-bin （apt 下载）
关系链：
    1. myos.iso是用grub-mkrescur生成的
        grub-mkrescur的默认行为如下：
            如果系统中安装了 BIOS 模块（grub-pc-bin），生成的 ISO 默认支持 ​BIOS 引导。
            如果系统中安装了 UEFI 模块（grub-efi-amd64-bin），生成的 ISO 默认支持 ​UEFI 引导。
            如果两者都已安装，生成的 ISO 支持 ​BIOS/UEFI 双引导
        但是当前的系统中没有grub-pc-bin，所以生成的不支持BIOS引导
    2. 执行qemu-system-i386 -cdrom myos.iso， qemu默认的引导方式是BIOS，所以导致无法引导myos.iso 
```