# centos7一键部署deploy_Kubernetes:v1.13.1脚本


觉得不错给个star哦！我还会持续更新脚本，但是我想跨代更新，等啥时候出V1.15我在写V1.14的。讲真K8S实在太快了更新的！
注意事项：

1、使用git clone的同志们需要将文件夹里面的所有文件cp 到/root下面。确保所有文件都在/root下面。实在不好意思哈！我不想改了！我把路径写成了/root了
然后只需要在修改base.config里面的固定参数即可。

2、给.sh结尾的脚本赋权限。

3、然后只需执行./deploy_k8s_master.sh就可以啦！

4、tail -f setup.log 查看日志

5、物理机不用说了，要是虚拟机cpu必须最少是2个哦！切记

6、常见centos7的内核可能是3+的，有可能需要升级内核。虽然我再脚本里面也写了（我注释掉了！），但是还要重启生效，脚本还得慢慢改，这次我没写那么细
所以大家在部署之前需要先升级一下内核（每台机器都执行哦！）。

# 具体实现步骤：

git clone https://github.com/luckman666/deploy_Kubernetes-v1.13.1.git

cd deploy_Kubernetes-v1.13.1/

chmod -R 755 .

mv * /root

cd /root

编辑base.config里面的参数

./deploy_k8s_master.sh

# 升级内核脚本

执行upgradeKernel.sh就可以将内核升级到4+了，脚本内容如下：
#!/bin/bash

setupkernel(){

 rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
 
 rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
 
 yum --enablerepo=elrepo-kernel install -y kernel-lt kernel-lt-devel
 
 grub2-set-default 0
 
 reboot
 
}

setupkernel

# base.config参数介绍：

masterIP：

masterip="192.168.1.107"

K8S版本：

k8s_version="v1.13.1"

服务器root密码

root_passwd=root123

多台主机的主机名前缀，主节点就叫k8s1，node叫k8s2依次后推

hostname=k8s

集群服务器IP地址

hostip='
192.168.1.107
192.168.1.108
192.168.1.109
'
再部署的时候严格按照我所给的示例参数写哦。换参数不要换格式，以免出错

# 部署完后进入到dashboard文件夹部署dashboard

cd dashboard

kubectl create -f .

然后查看部署情况以及登录的node节点端口

kubectl get service --all-namespaces | grep kubernetes-dashboard

例如结果：
kube-system   kubernetes-dashboard   NodePort    10.101.25.47   <none>        443:31660/TCP   22m
那么你就输入https://nodeIP:31660来登录
	
查看登录时候的token

kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

欢迎大家关注我个人的订阅号，会定期分享学习心得，相关案例信息!
全都是大白话，保证你们能看明白！
![index4](https://github.com/luckman666/devops_kkit/blob/master/gzh.jpg)


