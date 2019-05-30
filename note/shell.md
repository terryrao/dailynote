# 常用 Shell 命令


## 查找文件，并将文件移动到特定的目录 
```bash
	# mac OSX
	find . -iname "filename" | xargs -I {} mv {} /your/target/dir
```

