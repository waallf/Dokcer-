# Dokcer-
Dokcer使用
## 1、安装
### windows安装
  1、打开任务管理器--性能--CPU--虚拟化，确定是否已经开启
  ![微信图片_20231221090453](https://github.com/waallf/Dokcer-/assets/30369148/9521e126-5e87-4155-ac06-8cb956ed8782)
  2、开启Hyper-V
  在控制面版打开程序，然后点击启动或者关闭windows功能  ![微信图片_20231221090631](https://github.com/waallf/Dokcer-/assets/30369148/c743288b-6fc8-416d-a6ac-284461e14982)
 如果没有找到Hyper-V,在桌面新建一个Hyper-V.bat文件
 '''
 pushd "%~dp0"
 dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
 for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
 del hyper-v.txt
 Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
 '''
 然后以管理员身份运行
 3、开始安装windows 环境下的docker desktop installer.exe
 4、查看是否安装成功 docker --version
### linux安装docker
首先，检查系统是否已经安装了Docker。可以使用以下命令来检查：
whereis docker
如果输出类似于docker: /usr/bin/docker，则表示Docker已经安装在系统中。

如果之前安装过旧版本的Docker，可以使用以下命令卸载：
yum remove docker \ docker-client \ docker-client-latest \ docker-common \ docker-latest \ docker-latest-logrotate \ docker-logrotate \ docker-selinux \ docker-engine-selinux \ docker-engine \ docker-ce
安装Docker的依赖包。可以使用以下命令来安装：
yum install -y yum-utils device-mapper-persistent-data lvm2
添加Docker的软件源。可以使用以下命令来添加：
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
安装Docker。可以使用以下命令来安装：
yum install docker-ce
启动Docker服务。可以使用以下命令来启动：
systemctl start docker
设置Docker开机自启动。可以使用以下命令来设置：
systemctl enable docker
验证Docker是否安装成功。可以使用以下命令来验证：
docker --version
## 2、打包为tar压缩包
  1、生成requirement.txt文件:在代码的根目录运行 pipreqs ./ --encoding=utf8 --force
  2、在桌面新建一个文件夹内容如下
  ![微信图片_20231221091412](https://github.com/waallf/Dokcer-/assets/30369148/060998da-f26d-450a-90e8-35330e6659a3)

   app中的内容其实就是Python项目根目录以及子目录文件
  3、编写Dockerfile
  '''
  #基于的基础镜像
  FROM python:3.7.9
  #代码添加当前目录所有内容到code文件夹
  ADD . /code
  #设置code文件夹是工作目录
  WORKDIR /code
  #安装支持
  RUN pip install -r requirements.txt -i https://pypi.douban.com/simple/
  CMD ["python", "./app/predict/select_data_combine.py"]
  '''
  4、docker build -t hello-test .这里注意，hello-test后面有一个点，这里的hello-test是镜像的名称，随意命名、
  5、保存为镜像文件：docker save ubuntu>ubuntu.tar
  
## 3、解压为image并运行
  1、加载镜像文件 docker load -i redis5014.tar
  2、解压镜像文件，查看镜像 docker images
  3、docker run -it 镜像ID

## 常用命令：
   1、进入容器 sudo docker exec -it 容器ID bash
   2、退出容器 exit
   3、开启指定的容器 docker start 容器ID
   4、显示所有容器 docker ps -a
   5、开启容器失败，按照如下操作：
     '''
     docker exec -it 6198ef53d943 bash
     Error response from daemon: Container 6198ef53d9431a3f38e8b38d7869940f7fb803afac4a2d599812b8e42419c574 is not running

    docker commit 6198ef53d943
    sha256:ace7ca65e6e3fdb678d9cdfb33a7a165c510e65c3bc28fecb960ac993c37ef33

    docker run -it ace7ca65e6e bash
    root@72d38a8c787d:/#

     '''
  6、直接删除镜像 docker rmi 镜像ID
  7、强制删除镜像 docker rmi -f image_id
  8、删除容器 docker rm 容器ID
  9、如果删除不掉，先关闭容器，再删除 docker stop 容器ID
  10、强制删除容器 docker rm -f container_id

