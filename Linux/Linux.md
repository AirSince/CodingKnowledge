# Linux三把宝剑
grep、awk、sed，查找分段修改。



## Linux 命令提示符

```
[root@iZm5e8dsxce9ufaic7hi3uZ ~]# pwd
/root
```
+ <font color=green>root</font>：表示用户名
+ <font color=green>iZm5e8dsxce9ufaic7hi3uZ</font>：表示主机名
+ <font color=green>~</font>：表示目前所在目录为家目录，其中 <font color=green>root</font> 用户的家目录是 <font color=green>/root</font> 普通用户的家目录在 <font color=green>/home</font> 下
+ <font color=green>#</font>：指示你所具有的权限（ <font color=green>root</font> 用户为 <font color=green>#</font> ，普通用户为 <font color=green>$</font> ）
+ 执行<font color=green>whoami</font>命令可以查看当前用户名
+ 执行<font color=green>hostname</font>命令可以查看当前主机名
+ <font color=green>root</font>  是超级用户，具备操作系统的一切权限

## 快捷键
+ <font color=green>Ctrl + R</font> ：用于查找使用过的命令（history 命令用于列出之前使用过的所有命令，然后输入 ! 命令加上编号( !2 )就可以直接执行该历史命令）；
+ <font color=green>Ctrl + L</font>：清除屏幕并将当前行移到页面顶部；
+ <font color=green>Command + L</font>：清除一行；
+ <font color=green>Ctrl + C</font>：中止当前正在执行的命令；
+ <font color=green>Ctrl + U</font>：从光标位置剪切到行首；
+ <font color=green>Ctrl + K</font>：从光标位置剪切到行尾；
+ <font color=green>Ctrl + W</font>：剪切光标左侧的一个单词；
+ <font color=green>Ctrl + Y</font>：粘贴 Ctrl + U | K | Y 剪切的命令；
+ <font color=green>Ctrl + A</font>：光标跳到命令行的开头；
+ <font color=green>Ctrl + E</font>：光标跳到命令行的结尾；
+ <font color=green>Ctrl + D</font>：关闭 Shell 会话；

## 文件
<img src="image/linux-001.awebp" width = 80% height = 80% alt="图片名称" align=center />

### ls 命令
【参数】
+ -a 显示所有文件和目录包括隐藏的
+ -l 显示详细列表
+ -h 适合人类阅读的
+ -t 按文件最近一次修改时间排序
+ -i 显示文件的 inode （ inode 是文件内容的标识）

### cd 命令
```kotlin
cd /	--> 跳转到根目录
cd ~	--> 跳转到家目录
cd ..	--> 跳转到上级目录
cd ./home	--> 跳转到当前目录的home目录下
cd /home/lion	--> 跳转到根目录下的home目录下的lion目录
cd	--> 不添加任何参数，也是回到家目录
```

### 文件浏览和创建
#### cat
一次性显示文件所有内容，适合查看小的文件。
```kotlin
cat cloud-init.log
```
【参数】
+ -n 显示行号。

#### less
分页显示文件内容，适合查看大的文件。
```kotlin
less cloud-init.log
```
【快捷键】
+ 空格键：前进一页（一个屏幕）；
+ b 键：后退一页；
+ 回车键：前进一行；
+ y 键：后退一行；
+ 上下键：回退或前进一行；
+ d 键：前进半页；
+ u 键：后退半页；
+ q 键：停止读取文件，中止 less 命令；
+ = 键：显示当前页面的内容是文件中的第几行到第几行以及一些其它关于本页内容的详细信息；
+ h 键：显示帮助文档；
+ / 键：进入搜索模式后，按 n 键跳到一个符合项目，按 N 键跳到上一个符合项目，同时也可以输入正则表达式匹配。