# Linux的一些常见的命令

### 防火墙

关闭防火墙:systemctl stop firewalld

关闭防火墙开机启动：systemctl disable firewalld

防火墙状态：systemctl status firewalld



读取文件的内容（tail）：

```
// 默认显示 log 文件的最后 10 行
tail test.log
 
// 显示 log 文件的最后 10 行，同时跟踪名为文件的增长情况，直到您按下（Ctrl-C）组合键停止显示。
tail -f test.log
 
// 显示 log 文件的最后 n 行，同时跟踪名为文件的增长情况，直到您按下（Ctrl-C）组合键停止显示。
tail -nf test.log
 
// 显示文件的最后 10 行
tail -n 10 filename
 
// 文件的第 9 行不显示，显示第 10 行到末尾行
tail -n -10 filename
 
// 显示文件的第 10 行到末尾行
tail -n +10 filename
 
逆序显示filename最后10行。
tail -r -n 10 filename
 
// 显示第20行至末尾
tail +20 test.log
 
// 显示最后10个字符
tail -c 10 test.log
```



tail -f 文件名





动态读取：







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

