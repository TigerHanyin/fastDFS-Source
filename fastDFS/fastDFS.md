# fastDFS

#### 1.1什么是fastDFS?

FastDFS 是用 c 语言编写的一款开源的分布式文件系统。FastDFS 为互联网量身定制， 充分考虑了冗余备份、负载均衡、线性扩容等机制，并注重高可用、高性能等指标，使用 FastDFS 很容易搭建一套高性能的文件服务器集群提供文件上传、下载等服务。

FastDFS 架构包括 Tracker server 和 Storage server。客户端请求 Tracker server 进行文 件上传、下载，通过 Tracker server 调度最终由 Storage server 完成文件上传和下载。 

Tracker server 作用是负载均衡和调度，通过 Tracker server 在文件上传时可以根据一些 方法找到 Storage server 提供文件上传服务。可以将 tracker 称为追踪服务器或调度服务 器。 

Storage server 作用是文件存储，客户端上传的文件最终存储在 Storage 服务器上， Storageserver 没有实现自己的文件系统而是利用操作系统 的文件系统来管理文件。可以将 storage 称为存储服务器。 

服务端两个角色: 

Tracker:管理集群，tracker 也可以实现集群。每个 tracker 节点地位平等。收集 Storage 集群的状态。 

Storage:实际保存文件 

Storage 分为多个组，每个组之间保存的文件是不同的。每 个组内部可以有多个成员，组成员内部保存的内容是一样的，组成员的地位是一致的，没有 主从的概念。

小文件存储系统       作者：于庆  淘宝架构师  fastDFS         好处：1.冗余备份   根据内容做运算   一个文件只存一份  节约内存空间       2.横向扩容  只需要该fastDFS的配置       3.防止文件名重复      1.jpg   dasffdfxczgaf/jpg    1.jpg      sqweuyuyd.jpg |

### 1.2文件上传流程

######   | 工作流程：   

  0.仓库管理员定时巡逻查看仓库状态  

 1.小老板先找仓库管理远询问可用的仓库

 2.仓库管理员返回给小老板可用仓库地址  

 3.小老板拿到地址，找相应仓库存储数据   

 4.仓库返回给小老板存储凭证 |    

#####   | fastDFS工作流程  

 0.监听服务器定时查看存储服务器状态 

 1.客户端先找监听服务器获取可用的存储服务器地址   

 2.监听服务器返回可用的存储服务器地址给客户端  

 3.客户端根据返回的地址，存储相应数据  

 4.存储服务器返回给客户端存储凭证 ，此文件 ID 用于以后访问该文 件的索引信息。文件索引信息包括:组名，虚拟磁盘路径，数据两级目录，文件名。

### 1.3fastDFS 的安装

##### 1.3.1安装FastDFS依赖包

1. 解压缩libfastcommon-master.zip
2. 进入到libfastcommon-master的目录中
3. 执行**./make.sh**
4. 执行**sudo ./make.sh install**

##### 1.3.2安装FastDFS

1. 解压缩fastdfs-master.zip
2. 进入到 fastdfs-master目录中
3. 执行 **./make.sh**
4. 执行 **sudo ./make.sh install**

##### 1.3.3配置跟踪服务器tracker

1. ```shell
   sudo cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
   ```

2. 在/home/itcast/目录中创建目录 fastdfs/tracker      

   ```shell
   mkdir -p /home/itcast/fastdfs/tracker
   ```

3. 编辑/etc/fdfs/tracker.conf配置文件    sudo vim /etc/fdfs/tracker.conf

​          修改 base_path=/home/itcast/fastdfs/tracker

##### 1.3.4配置存储服务器storage 

1. ```
   sudo cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
   ```

2. 在/home/itcast/fastdfs/ 目录中创建目录 storage

   ```shell
   mkdir –p /home/itcast/fastdfs/storage
   ```

3. 编辑/etc/fdfs/storage.conf配置文件  sudo vim /etc/fdfs/storage.conf

   修改内容：

   ```shell
   base_path=/home/itcast/fastdfs/storage
   store_path0=/home/itcast/fastdfs/storage
   tracker_server=自己ubuntu虚拟机的ip地址:22122
   ```

##### 1.3.5启动tracker和storage

进入到/etc/fdfs/下面执行以下两条指令

sudo  fdfs_trackerd  /etc/fdfs/tracker.conf
sudo fdfs_storaged  /etc/fdfs/storage.conf

##### 1.3.6测试是否安装成功

1. **sudo cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf **
2. 编辑/etc/fdfs/client.conf配置文件  **sudo vim /etc/fdfs/client.conf**

修改内容：

```shell
base_path=/home/itcast/fastdfs/tracker
tracker_server=自己ubuntu虚拟机的ip地址:22122
```

3. 上传文件测试(fastDHT)

   sudo fdfs_upload_file /etc/fdfs/client.conf 要上传的图片文件 

   如果返回类似**group1/M00/00/00/rBIK6VcaP0aARXXvAAHrUgHEviQ394.jpg **的文件id则说明文件上传成功