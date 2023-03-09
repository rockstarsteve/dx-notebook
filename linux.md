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









## bash中 2>&1 & 的详细解释

```
1、首先，bash中0，1，2三个数字分别代表STDIN_FILENO、STDOUT_FILENO、STDERR_FILENO，即标准输入（一般是键盘），标准输出（一般是显示屏，准确的说是用户终端控制台），标准错误（出错信息输出）。

0	1	2
标准输入	标准输出	标准错误
2、输入输出可以重定向，所谓重定向输入就是在命令中指定具体的输入来源，譬如 cat < test.c 将test.c重定向为cat命令的输入源。
输出重定向是指定具体的输出目标以替换默认的标准输出，譬如ls > 1.txt将ls的结果从标准输出重定向为1.txt文本。有时候会看到如 ls >> 1.txt这类的写法。 > 和 >> 的区别在于：> 用于新建而>>用于追加。

3.到了此时，你应该也能猜到2>&1&的用意了。不错，2>&1就是用来将标准错误2重定向到标准输出1中的。此处1前面的&就是为了让bash将1解释成标准输出而不是文件1。至于最后一个&，则是让bash在后台执行。

标准错误重定向到标准输出并且在后台执行。
————————————————
版权声明：本文为CSDN博主「鐽」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_43264873/article/details/104861242
```

















