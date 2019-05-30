# 第二章 命令之乐

## 1 cat 命令
1. 将标准输入文件内容与标准输入拼接起来
```bash
    echo 'Text through stdin' | cat - file.txt
```
2. 压缩空白行
```bash
    # 压缩连续空白行
    cat -s file
    # 移除空白行
    cat file | tr -s '\n'
```
## 2. 录制与回放终端会话
script 与 scriptreplay 参与录制回放终端会话
```bash
# 录制
script -t 2> timing.log -a output.session
type commands;
...
...
exit

# 回放
scriptreplay timing.log output.session

```
在多个用户之间进行广播

（1）在Terminal1 中输入以下命令:
```bash
mkfifo scriptfifo
```
(2) 在 Terminal2 中输入以下命令：
```bash
cat scriptfifo
```
(3)返回 Terminal1 中，输入以下命令：
```bash
script -f scritpfifo
commands
```

## 3. 文件查找与文件列表
```bash
    find base_path
    # 打印文件和目录的列表
    find . -print

    # 使用以 `\0`作为定界符
    find . -print0
    
    # 查找 /home/user 目录下 所有以.txt 结尾的文件，打印出来
    # 如果忽略大小写 用 -iname
    find /home/user -name "*.txt" -print

    # 匹配多个条件的一个，可以使用 -o or操作命令
    # \( \) 会将里里面的内容视为一个整体
    find . \( -name "*.txt" -o -name "*.pdf" \) -print
     
```
文件类型
| 文件类型 | 类型参数 |
| ----     | ----     |
| 普通文件 | f        |
| 符号连接 | l        |
| 目录     | d        |
| 字符设备 | c        |
| 块设备   | b        |
| 套接字   | s        |
| Fifo     | p        |

时间戳

计量时间为天
* 访问时间 -atime
* 修改时间 -mtime
* 变化时间 -ctime

计量时间为分钟
- amin (访问时间)
- mmin（修改时间）
- cmin （变化时间）

```bash
# 打印出最近七天内被访问过的所有文件
find . -type f -atime -7 -print
# 打印七天前被访问过的所有文件
find . -type f -atime 7 -print
# 打印出访问时间超过七天的所有文件
find . -type f -atime +7 -print
```
```bash
# 大于 2KB 的文件
find . -type f -size +2k

# 小于 2KB 的文件
find . -type f -size -2k

# 等于 2KB 的文件

find . -typ f -size 2K
```

删除匹配的文件
```bash
find . -type f -name "*.swp" -delete

# 打印出所有权限为 644 的文件
find . -type f -perm 644 -print

# 打印出用户所拥有的文件
find . type f -user xxx -print
```

结合find 执行命令。
```bash
# 将当前目录下所有属于 root 用户的文件改为 xxx 
# {}是一个特殊字符串，与 -exec 结合使用，对于每个
# 匹配的文件，{}都被替换成相应的文件名
find . -type f -user root -exec chown xxx {} \;
```
-exec 只能执行单一的命令，不过可以将多个命令打包到
一个 sh 文件中，然后用 -exec 执行该脚本

find 跳过指定目录
```bash
find devel/source_path \( -name ".git" -prune \) -o \( -type f -print\)

```
## 4. xargs

xargs 擅长将标准输入的内容转成命令行参数。通用形式：
```bash
command | xargs
```
1. 将多行变成单行输出

```bash
cat example.txt | xargs
```
2. 将单行变成多行
```bash
#  -n 表示每行多少个参数 下例为 3
cat example.txt | xargs -n 3
```
3. 指定分割符 -d

```bash
# 注意 在 mac 下不好使
echo "splitXsplitXsplitXspiltX" | xargs -d X
```
```bash
# 统计代码行数
find . -type f -name "*.c* -print0 | xargs -0 wc -l
```
4. 结合 stdin，巧妙运用 while 语句和子 shell
```bash
cat files.txt | (while read arg; do cat $arg; done)
# 相当于
cat files.txt | xargs -I {} cat {}
# 也相当于
cat files.txt | xargs cat {}
```
## 5. 用 tr 进行转换
tr 只能通过 stdin ，而无法通过命令行来接收输入，它的调用
格式如下：
```bash
tr [options] set1 set2
```
1. 将输出字符由大写改为小写
```bash
echo "HELLO WHO IS HERE" | TR 'A-z' 'a-z'
```
2. tr 删除字符串
```bash
cat file.txt | tr -d '[set1]'
```

## 6. 排序
1. sort 可以用来排序
```bash
# 按数字排序
sort -n file.txt
# 按逆序进行排序
sort -r file.txt
# 按月份进行排序

sort -M months.txt

```
2. uniq 可以用来消除重复
```bash
sort unsorted.txt | uniq
```
只显示唯一一行
```bawh
uniq -u unsorted.txt 
```
统计出现的次数
```bash
sort unsorted.txt | uniq -c
```
3. 获取扩展名
在以 "文件名.扩展名" 结构中可以使用 '%' 操作将文件名取出来。
`${VAR%.*}` 的含义是：
* 从 `$VARIABLE` 中删除位于%右侧的通配符所匹配的字符串。
* 给 `VAR` 赋值
% 属于非贪婪操作，从右到左找出匹配通配符的最短结果。
%% 贪婪操作，匹配符合条件的最长的字符串。
`#` 与 `%` 类似，方向是从<font coler='red'>左到右</font>
'#' 是非贪婪匹配
`##` 是贪婪匹配

## 7. 互交化输入
```bash
#!/bin/bash
# filename=interactive.sh
read -p "Enter number : " no;
read -p "Enter name : " name;

echo You hae entered $no, $name;
```
输入
```bash
echo -e "1\nhello\n" | ./interactive.sh
```
也可以将命令输入到文件中，然后再从文件重定向到 `interactive.sh` 中
```bash
echo -e "1\nhello\n" > input.data
./interactive.sh < input.data
```


