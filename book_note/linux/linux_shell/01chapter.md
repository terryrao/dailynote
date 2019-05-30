# 第一章
## 1.1 简介
1. linux 环境下任何脚本语言都需要一个被称为 shebang 的特殊行为作为起始。
这行中，字符 `#!` 被置于解释器之前。/bin/bash  是 Bash 的目录。
```bash
    #!/bin/bash
```
如果只需要用 `sh` 命令运行，shebang 是没有什么用处的。

2. 如果脚本需要独立运行，需要具备运行时权限。
```bash
    chmod a+x script.sh
```

3. 命令之间是通过分号来分隔的
```bash
    cmd1 ; cmd2
```

4. echo 是打印终端的基本命令
```bash
    echo "welcome to bash"
```
5. "" 与'' 的区别： ''是原样输出 ""中所有的特殊字符需要转义

6. printf 与 c 语言中的 printf 用法一致

%-5s 代表一个宽度为5的左对齐的字符串，如果没有'-'表示右对齐
%-4.2f .2表示保留两位小数

7. 打印彩色输出
```bash
    echo -e "\e[1;31m This is red Text \e[0m"
    # e[1;31 将颜色设置为红包，\e[0m 将颜色重新回置。
```
## 1.3 变量及环境变量
1. 查看某个命令相关的环境变量的通用模式。
```bash
    cat /proc/$PID/environ
```
查看某个命令的进程使用以下命令：
```bash
    pgrep bash
    # 如结果显示 3460
    cat /proc/3460/environ
    #或者 
    # cat /proc/3460/environ | tr '\0' '\n'
```

2. 变量赋值
```bash
    #给变量赋值
    var=value
    # 注意 var = value 的意思是 var 和 value 是否相等的意思，注意空格
    #显示变量内容
    echo $var
```


3. 设置环境变量
```bash
HTTP_PROXY=http://127.0.0.1
export HTTP_PROXY 
echo $HTTP_PROXY
```

4. 获取字符串长度
```bash
    length=${#var}
```
5. 识别当前的 shell 版本
```bash
    echo $SHELL
    echo $0
```
6. 检查是否为超级用户
```bash
# root 的UID 是 0
if [ $UID -ne 0 ]; then
echo Non root user. Please run as root.
else
echo "Root user"
fi
```
6. 修改 Bash 提示字符串 (useranme@hostname:~)
```bash
    # 修改 PS1 变量来改变提示文本
    # 默认在 ~/.bashrc 或者 ~/.zshrc 中
    cat ~/.bashrc | grep PS1
```
7. let 命令可以直接进行基本的算术操作，且变量名之前不用再加`$`。
```bash
    no1=4;
    no2=5;
    let result=no1+no2
    echo $result
    # 自加操作
    let no1++;
    # 自减操作
    let no2--;
    #操作符[] 的使用方法与 let 命令类型，
    result=$[ no1 + no2 ]
    #也可以使用 (()) ，需要在前面加上`$`
    #expr 同样可以用于基本算数运算
    result=`expr 3 + 4`
    result=$(expr $no1 + 5)
    # 浮点运算
    echo "4 * 0.56" | bc
    no=54;
    result=`echo "$no * 1.5"" | bc`
    # 设定小数精度
    echo "scale=2;3/8" | bc
    # 进制转换下面是转成二进制
    echo "obase2;100" | bc
    # 平方及平方根
    echo "sqrt(100)" | bc 
    echo "10^10" | bc 
```
8. 文件描述及重定向
    `>` 及 `>>` 不同之处是 `>` 会将文件清空，`>>` 是以追加的方式写入数据。
```bash
    # 将错误重定向到文件
    cmd 2>&1 output.txt
    # or
    cmd &> output.txt
    # 如果正常输出不变，把错误重定向到指定文件
    cat a* 2> err.txt
    # 如果把错误抛弃
    cat * 2> /dev/null
```
当对 `stderr` 或者 `stdout` 重写向时，这些文本已经重定向到文件之中，就不能
能过管道`|` 命令接收文件了。
不过有个方法可以避开这个限制，用 `tee` 来实现将文本重定向到文件中的同时，
继续在终端打印，通用模式 `command | tee File1 File2`
```bash
   cat a* | tee out.txt | cat -n
```


9. 将文件重定向到命令
```bash
    cmd < file
```
10. 重定向脚本内部的文本块

```bash
#!/bin/bash
# 执行该脚本会将文本打印到 log.txt 中
cat <<EOF>log.txt
LOG FILE HEADER
this is a test log file 
Function: system statistics
EOF

```
11. 对别名进行转义，这样就可以忽略别名
```bash
    \command
```
12. 获取终端信息
```bash
    tput cols
    tput lines
    # 打印出当前终端名：
    tput longname
    # 将光标移到到(100,100)处
    tput cup 100 100
    # 将文本前景色设置为白色
    tput serf no
    # 设置下划线的起止：
    tput smu1
    tput rmu1
    # 删除当前光标位置到行尾的所有内容：
    tput ed
    
    # 输入密码的时候不显示输出内容，可以使用 `stty` 来实现。
    #!/bin/sh
    #Filename:password.sh
    echo -e "Enter password: "
    stty -echo
    read password
    stty echo
    echo password read.
    
    
```

13. 时间 
```bash
    date
    # 秒数
    date +%s 
    # 睡眠
    sleep no_of_seconds.
```
```bash
#Filename: sleep.sh
echo -n Count:
tput sc
while true;
 do
     if [ $count -lt 40 ];
     then let count++;
         sleep 1;
         tput rc;
         tput ed;
         echo -n $count;
     else
         exit 0;
     fi
 done
 ```
14. 调试 运行时加 `-x` 标识会打印每一行输出到控制台
```bash
    bash -x script.sh
``` 
也可以使用内建的方式来禁止或者启动调试打印
```bash
#!/bin/bash
# filename: debug.sh
for i in {1..6}
do
    set -x
    echo $i
    set +x
done
echo "Script executed"
```