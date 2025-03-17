# linker script
## 作用
控制链接器ld的行为
## ld 脚本中的关键字
### ✅ 全局命令 (Global Commands)
| 关键字                 | 作用                                                 |
|----------------|---------------------------------------------------|
| `ENTRY(symbol)`       | 指定程序入口点 (通常是 `_start`) |
| `OUTPUT_FORMAT(format)` | 指定输出文件格式 (如 ELF, Binary, etc.) |
| `OUTPUT_ARCH(arch)` | 指定目标架构 (如 `riscv`, `arm`, `x86_64`) |
| `SEARCH_DIR(path)` | 添加库文件搜索路径 |
| `INCLUDE(file)`          | 引入其他 `ld` 脚本 |

---

### ✅ 内存和段布局 (Memory and Sections)
| 关键字             | 作用 |
|----------------|---------------------------|
| `SECTIONS`          | 定义段布局 |
| `MEMORY`            | 定义内存区域 (RAM, ROM, Flash) |
| `PHDRS`                 | 定义 ELF 程序头 (Program Header) |
| `REGION_ALIAS(name, region)` | 定义内存区域别名 |

---

### ✅ 表达式和操作符 (Expressions and Operators)
| 关键字                 | 作用 |
|----------------|--------------------|
| `ALIGN(n)`                | 地址对齐 |
| `AT(lma)`                    | 控制段的加载地址 (Load Memory Address) |
| `ADDR(section)`         | 获取段的起始地址 |
| `SIZEOF(section)`     | 获取段的大小 |
| `ORIGIN(region)`       | 获取内存区域的起始地址 |
| `LENGTH(region)`     | 获取内存区域的长度 |
| `LOADADDR(section)` | 获取段的加载地址 |
| `KEEP(section)`            | 强制保留某个段 (防止被优化掉) |
| `PROVIDE(symbol = value)` | 定义符号 |
| `PROVIDE_HIDDEN(symbol = value)` | 定义隐藏符号 |
| `ASSERT(condition, message)` | 触发编译时检查 |
| `SORT()`                          | 对输入段进行排序 |
| `CONSTANT()`                  | 定义常量 |

---

### ✅ 内置变量 (Built-in Variables)
| 变量                     | 作用 |
|----------------|--------------------|
| `.` (dot)               | 当前地址 |
| `_start`                | 程序入口符号 |
| `_end`                  | 程序镜像结束地址 |
| `__bss_start`      | BSS 段起始地址 |
| `__heap_start` / `__heap_end` | 堆区域 |

## 段的定义
```
SECTION
{
    # 定义名为xxx的段
    xxx : {

    }
}
```

## 变量的定义
```
# 定义xxx变量
xxx = 0x80000000;
```

### Example
```
BASE_ADDRESS = 0x80200000; #定义符号变量 BASE_ADDRESS
SECTIONS    # 定义 SECTIONS
{
   . = BASE_ADDRESS;  # dot符号，代表当前应处的位置，这里表示SECTIONS开头在BASE_ADDRESS处
   skernel = .;  # 定义符号变量skernel，等于dot，也就是等于当前地址

   stext = .; # 定义符号变量stext，等于dot

   .text : {  # 定义.text段的布局
      *(.text.entry)   # .text.entry 在 .text 开头
      *(.text .text.*) # 匹配所有.text 段，所有.text.* 段
   }

   ...
}
```