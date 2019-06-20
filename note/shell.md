# 常用 Shell 命令


## 查找文件，并将文件移动到特定的目录 
```bash
	# mac OSX
	find . -iname "filename" | xargs -I {} mv {} /your/target/dir
```

## 无密码 ssh 登录其它节点
```bash
	ssh-keygen -t rsa
	ssh-copy-id user@hostname:port
```
另外一种是将主机的 `.ssh` 目录下的 `.pub` 文件里的公钥放到要登录的机器的 `~/.ssh/authorized_keys` 文件中，同时需要修改 sshd_config 的
`AuthorizedKyesFile ` 注释
