# Redis基础安装操作-windows版

> 2018/12/7 16:27

**一、下载**

​      redis官方没有提供windows版本，需要从微软的git[下载releases](https://github.com/MicrosoftArchive/redis/releases)版

**二、安装，启动**

​    1.解压出来 启动服务

​    ![img](http://tnblog.net/arcimg/cz/252c568939894083ad744c7c8ce5526b.png)

​    可能会双击会闪一下就没了或者**启动成功也没办法加载指定的配置文件比如配置了密码也不会生效**，最好执行命令

​    redis-server redis.windows.conf  可以加个参数 --maxheap  200m 设置redis 可以最大内存

​    ![img](http://tnblog.net/arcimg/cz/7ac6802a285d4e24b49889f10bf23f50.png)

​    这样启动 每次都会带一个命令窗口 关闭之后服务也停止了， 可以把redis服务化来解决

2.redis服务化

​    命令：**redis-server.exe --service-install redis.windows.conf** 

​    ![img](http://tnblog.net/arcimg/cz/cca6c1e24ba64c398cea0a93ef99eacf.png)

​    redis-server --service-uninstall 卸载服务



**三、指定服务名的安装卸载**

​     指定服务名安装：redis-server --service-install redis.windows.conf --service-name redis6389

​     指定服务名卸载：redis-server --service-uninstall --service-name redis6389

​     **装完记得启动**





**四、.密码设置 修改**

​    可以在客户端通过命令设置密码config set requirepass 123456来设置密码

​    ![img](http://tnblog.net/arcimg/cz/fc3d7fdf26a84b018a4042eeef0bb625.png)



​    但是重启服务之后密码就会失效，所以需要在配置文件中redis.windows.conf设置密码（配置文件对应启动时 接入的配置文件）

​    ![img](http://tnblog.net/arcimg/cz/39bb28d6289e45b6aa8cf31205b0f7be.png)





**五、基本值设置 生命周期 命令**

​    **![img](http://tnblog.net/arcimg/cz/3fb3f52c6b5745799784fce1eb1839ea.png)**



​    连接客户端：redis-cli -p 6379 -a 密码     

​    远程连接的话 需要加上-h 参数：redis-cli -h 127.0.0.1 -p 6379 -a 密码

​    get test 获取键为test的值

​    set test 111 ex 60 设置键为test 值为111 60秒后过期

​    ttl test 查看剩余存在时间