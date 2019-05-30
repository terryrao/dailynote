# jenkins 

## 1. 添加git 仓库

```bash
	# 登录用户
	su -s /bin/bash jenkins
	# 加添 rsa 密钥 按提示走下去
	ssh-keygen
	# id_rsa.pub 中的公钥存放在 gitblab 中	
	git ls-remote -h git@172.15.105.160:linjd/txdata.git HEAD
	# yes 将 gitlab 的公钥加入本地

```
