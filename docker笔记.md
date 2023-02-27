# docker命令



- 查看镜像
  - docker images
- 运行镜像
  - docker run -it（以标准输入输出交互启动） -d（启动到后台） --name（给容器起名称）
- 查看容器
  - docker ps  -a（查下运行过的和没有的一起）  
- 停掉容器
  - docker stop
- 删除容器
  - docker rm
- 删除镜像
  - docker rmi
- 启动重启容器
  - docker start|restart
- 重命名容器
  - docker rename
- 去到运行的容器的控制台
  - docker exec -it 容器 /bin/bash
- 查看内存消耗
  - top
- 停止所有容器
  - docker stop $(docker ps -aq)





## docker目录挂载（linux下）

```
docker run -p 6379:6379 -v /c/docker.image.data/redis/data:/data -d redis:5.0.5 redis-server --appendonly yes
```

你的目录是`C:\docker.image.data\redis\data` 需要写成`/c/docker.image.data/redis/data`





## WSL2下安装ubuntu及docker(https://www.jianshu.com/p/5416dcef4bd7)

	-  wsl2中的关闭：net stop LxssManager
	-  wsl2中的启动：net start LxssManager



- ununtu(https://zhuanlan.zhihu.com/p/147493558)

  - 更新 sudo apt-get update

- 下载docker

  ```
  # install docker
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  
  if [ ! $(getent group docker) ];
  then
      sudo groupadd docker;
  else
      echo docker user group already exists
  fi
  
  sudo gpasswd -a $USER docker
  sudo service docker restart
  
  rm -rf get-docker.sh
  ```

  

## ununtu

- 切换到root用户：su root
- 

### 启动docker

ubuntu下的命令：	service docker start





## 进入wsl2目录
- \\wsl$\
- explorer.exe .



## docker-compose命名

- 后台启动docker-compose.yml
  - docker-compose up -d
- 关闭清理
  - docker-compose down





