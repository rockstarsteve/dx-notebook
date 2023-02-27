# Linux的一些常见的命令

## 防火墙

关闭防火墙:systemctl stop firewalld

关闭防火墙开机启动：systemctl disable firewalld

防火墙状态：systemctl status firewalld



## 目录方面：ls,mkdir

ls：显示指定目录的文件和目录。

cd：切换目录。cd - 是回到上一次目录

mkdir： 创建目录。

cp：复制文件和目录。cp -r 

mv：移动和重命名。

rm：删除文件和目录。



## 文件查看

读取文件的内容（tail）：

tail -f 文件名

```
// 默认显示 log 文件的最后 10 行
tail test.log
 
// 显示 log 文件的最后 10 行，同时跟踪名为文件的增长情况，直到您按下（Ctrl-C）组合键停止显示。
tail -f test.log
 
// 显示 log 文件的最后 n 行，同时跟踪名为文件的增长情况，直到您按下（Ctrl-C）组合键停止显示。
tail -nf test.log
```



## 文件权限

chmod：修改文件夹的权。chmode -R 777 test.txt



## 压缩解压

### gz（建议不用）

	- 压缩：gzip test.txt
	- 解压：gzip -d test.txt.gz

### tar

- 压缩：tar -cf all.tar test.txt
- 解压：tar -xf all.tar







**发送脚本**

```
#!/usr/bin/expect -f 


#用户名
set user ubuntu
#密码
set passwd 2020hns@css#
#终端服务器IP
set host 175.178.220.88
#终端服务器端口
set port 22
#本地路径
set local_path /project/jenkins/opt/jenkins/data/workspace/hello-project/target/test.txt
#终端服务器路径
set remote_path /project/test/

spawn scp -r /project/jenkins/opt/jenkins/data/workspace/hello-project/target/tets.sh ubuntu@175.178.220.88:/project/test/
set timeout 5
expect {
"Connection refused" exit
"Name or service not know" exit 
#表示匹配到yer/no时就发送字符串yes\n到该进程里
"yes/no" { send "yes\n";exp_continue }
#匹配到password时就发送passwd\n到进程里
"*assword" { send "$passwd\n" }
 }
expect eof
```

