# Configure
## 重要文件&作用
1. configure.ac : 被Autoconf解析，生成configure文件
2. configure ： 由Autoconf解析configure.ac生成，用于检测系统环境，配置编译环境等,本质是个shell脚本
3. Makefile.am: 被AutoMake 解析，生成Makefile.in文件
4. Makefile.in : 由AutoMake生成，但还不是最终的Makefile文件，属于中间文件，它的特定就是在这个文件中包含很多的占位符，比如@CC@, 这些占位符当前还没有具体的值，configure执行后，会替换这些占位符，并生成最终的Makefile
5. Makefile： 由configure和Makefile.in联合生成，用于最终的make执行

## 其他文件
1. config.status: 当执行完一次configure之后，configure会根据host环境和指定的参数，进行一系列检查，用户输入的参数和一些检查的结果会保存到config.status当中
2. config.log : 记录configure脚本执行的过程

## 操作
1. 重新生成config.status或者Makefile： 重新输入configure + 参数 即可
2. 修改了configure.ac或者Makefile.am : 分别通过对应工具生成