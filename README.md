# Docker ELK(含有x-pack)

## 环境

docker，docker-compose

## 使用方法

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

