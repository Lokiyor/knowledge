## 1、确认Redhat上装好了NFS软件包，在终端输入

>[root@localhost examples]# rpm -q nfs-utils
nfs-utils-1.0.9-24.el5

如果出现以上结果，表示已经安装好了NFS，否则，请先安装nfs-utils软件包。

 

## 2、配置NFS服务器

>[root@localhost examples]# vi /etc/exports

打开配置文件后，一般空空如也，自己写，写入以下配置信息：

>/home/example/rootfs 192.168.2.*(rw,sync,no_root_squash)

解析：

/home/example/rootfs          -->共享的文件夹路径
192.168.2.*                         -->允许访问的IP号，当然也可以具体指定一个，比如192.168.2.11

 (rw,sync,no_root_squash)  -->权限等，具体参数的意思可以百度

所以配置信息还是比较简单。

 

## 3、启动/重启NFS服务器

不管NFS事先有没有启动，都可以用重启命令，如下：

>[root@localhost examples]# /etc/init.d/nfs restart

 

OK，至此服务器端配置完毕，接下来在客户端开发板上使用，即所谓的挂载文件系统。

在挂载之前，需确认网络是否通了。将网线连上，通过ping命令确保网络正常，比如服务器IP设置为192.168.2.22，开发板IP设置为192.168.2.11，
两者子网掩码均为255.255.255.0，这样在开发板上通过命令ping 192.168.2.22即能看到响应。

 

## 4、挂载文件系统

在开发板上的mnt文件夹下新建nfs文件夹，用于映射服务器上共享的文件夹，当然你也可以在其他地方建，只要输入命令正确，命令如下：
mount -t nfs 192.168.2.22:/home/example/rootfs /mnt/nfs

解析：

mount -t nfs            -->挂载命令，表示挂载NFS共享的文件夹

192.168.2.22:/home/example/rootfs  -->服务器端共享的文件夹路径，格式为 IP:/共享文件夹路径

/mnt/nfs                  -->映射到本地的文件夹，即客户端开发板上的文件夹。

 

注：如果输入命令，出现如下错误“svc: failed to register lockdv1 RPC service (errno 111)  ”，则改用如下命令：

>mount -t nfs -o nolock 192.168.2.22:/home/example/rootfs /mnt/nfs

 

## 5、挂载完毕，使用

挂载完毕后，在服务端对共享文件夹里的操作，均能在客户端中映射的文件夹中看见。

Exa. 

 往服务端共享文件夹中拷贝一个文件，
 >[root@localhost examples]# cp test.c  rootfs/

 则在客户端能也看见，在客户端命令：
 >[root@localhost led]# cd /mnt/nfs/
 
 >[root@localhost nfs]# ls test.c