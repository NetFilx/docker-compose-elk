# Docker ELK(含有x-pack)

## 环境

docker，docker-compose

## 使用方法

### CentOS 用户

**SELinux**

On distributions which have SELinux enabled out-of-the-box you will need to either re-context the files or set SELinux into Permissive mode in order for docker-elk to start properly. For example on Redhat and CentOS, the following will apply the proper context:

```shell
$ chcon -R system_u:object_r:admin_home_t:s0 docker-elk/
```

### 启动

终端中进入docker-compose.yml的同级目录，在终端中输入

```shell
docker-compose up
```

输入

```shell
docker-compose up -d
```

后台启动

### 日志收集方式

终端输入

```shell
nc localhost 5000 < /Users/limbo/Desktop/测试数据/test/test.log
```

### 查看输出

打开浏览器输入localhost:5601，

user：elastic

password:changeme

新建一个index即可看到输出

##坑点

定义数据卷的时候注意要挂载的文件不能是一个文件，必须要是一个文件夹

