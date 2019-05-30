# 第三章 以文件之名
## 1. 文件的交集与差集
使用 comm 可以对文件求并交差集

## 2. 文件权限
ls -al 第一列的内容
-rw-r--r-- 
1. 第一代表文件类型
2. 第2到4代表用户权限
3. 第5-7代表用户组权限
4. 第8-10代表其它用户权限

每组权限分为读(r)写(w)执行(x)的权限。

对于用户来说，x 位置有一个 setuid(S)的特殊权限，允许用户以其拥有者的
权限执行文件，即使这个可执行文件是由其它用户运行的。

对于用户组来说，x 的位置有一个setgid 的权限。
对于目录来说有些特殊，
- r 允许读取目录中的文件和子目录中的列表
- w 允许在目录创建或者删除文件或者目录
- x 是否可以访问目录中的文件和子目录
- t x 的位置如果被一个t 代替，表示这个目录只能由创建者删除

```bash
# 修改权限
chmod u=rwx g=rw o=r filename
# 添加权限 a 表示全部 u 表示用户 g 表示组 o 表示其它
# 全部添加执行权限
chmod a+x filname
# 全部删除执行权限
chmod a-x filname
```
文件信息命令： file 
文件对比命令: diff
补丁命令：
```bash
diff -u version1.txt version2.txt > version.patch
patch -p1 version1.txt < version.patch
# 撤销就再次执行上面的命令
patch -p1 version1.txt < version.patch
```
diff 也可以生成目录差异信息
```bash
diff -Naur directory1 directory2
# -N 所有缺失的文件视为空文件
# -a 将所有的文件视为文本文件
# -U 生成一体化输出
# -r 遍历目录下的所有文件
```
head file 读取文件的头部
tail file 读取文件的尾部
```bash
cat text | head
# 打印前 n 行
head -n 4 file
#打印除最后N行之外所有的行
head -n -N file 
# -f 追加新的内容
tail -f /var/log/messages 
```

只列出目录的方法
```bash
ls -d */
ls -F | grep "/$"
ls -l | grep "^d"
find . -type d -maxdepth 1 -print
``` 

命令行中使用 pushd popd 进行目录跳转
```bash
# 目录入栈
pushd /usr/local
pushd /etc/aliases
pushd /bin
# 查看栈中有多少目录 
dirs
# 跳到指定索引的目录
push +2
# 删除指定编号的目录
popd +n 
```

统计行数，单词数
```bash
wc -l file
cat file | wc -l
# 统计单词数
wc -w file
```
打印目录树 tree