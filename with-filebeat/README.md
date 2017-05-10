# docker-compose+ELK+Filebeat

1. elasticsearch，logstash，kibana这三个docker-compose.yml可以写在一起的



## 相关配置

## Elasticsearch 配置

Elasticsearch 作为日志的存储和索引平台，他的 docker-compose 配置文件如下：

```yaml
version: '2'
services:
  elasticsearch:
    image: elasticsearch:2.2.0
    container_name: elasticsearch 
    restart: always
    network_mode: "bridge"
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
       - ./data:/usr/share/elasticsearch/data
```

其中，主机向外暴露的端口 `9200` 作为 对外提供服务的 HTTP 端口；`9300` 作为交互的 TCP 端口；
**将日志索引文件的存储目录挂载到主机的目录，防止因容器挂掉活重启导致历史日志文件丢失。**

配置好 `docker-compose.yml` 文件后, 运行 `docker-compose up -d` 便以 daemon 方式启动。用 `docker ps` 命令查看启动状况.

## 3. Logstash 配置

Logstash 作为日志的加工处理平台，有很多的插件可以配置，他的 docker-compose 配置文件如下：

```yaml
version: '2'
services:
  logstash:
    image: logstash:2.2.0-1
    container_name: logstash 
    restart: always
    network_mode: "bridge"
    ports:
      - "5044:5044"
      - "4560:4560"
      - "8080:8080"
    volumes:
      - ./conf:/config-dir
      - ./patterns:/opt/logstash/patterns
    external_links:
      - elasticsearch:elasticsearch
    command: logstash -f /config-dir
```

其中暴露的端口 `5044` 用于接收来着 Filebeat 收集的日志数据； `4569` 用于接收来着 Log4j 的日志数据；`8080` 用于接收来自插件 Logstash-input-http 的日志数据；

挂载 `conf` 目录，用于添加我们自定义的配置文件，`patterns` 用于添加我们自定义的 grok 规则文件。

同时，和 `elasticsearch` 容器连接用来传送数据。

配置好 `docker-compose.yml` 文件后, 运行 `docker-compose up -d` 便以 daemon 方式启动。用 `docker ps` 命令查看启动状况.

## 4. Filebeat 配置

Filebeat 安装在每一个需要收集日志的主机上收集指定的日志，他的 docker-compose 配置文件如下：

```yaml
version: '2'
services:
  filebeat:
    image: olinicola/filebeat:1.0.1 
    container_name: filebeat 
    restart: always
    network_mode: "bridge"
    extra_hosts:
      - "logstash:127.0.0.1"
    volumes:
      - ./conf/filebeat.yml:/etc/filebeat/filebeat.yml
      - /data/logs:/data/logs
      - /var/log:/var/host/log
      - ./registry:/etc/registry
```

其中以 `extra_hosts` 的方式，指定 Logstash 的位置；挂载配置文件目录 `conf`；挂载本机的日志目录 `/data/logs`; 挂载本机的系统日志目录 `/var/log`; **挂载 registry 目录，用来确定 Filebeat 读取文件的位置，防止 Filebeat 因重启或挂机导致又从头开始读取日志文件，造成日志数据重复。**

## 5. Kibana 配置

Kibana 的配置最为简单，仅作为数据可视化的平台，提供 HTTP 访问服务即可。他的 docker-compose 配置文件如下：

```yaml
version: '2'
services:
  kibana:
    image: kibana:4.4.0
    container_name: kibana 
    restart: always
    network_mode: "bridge"
    ports:
      - "5601:5601"
    external_links:
      - elasticsearch:elasticsearch
```

其中连接到 `elasticsearch` 容器用来获取数据；暴露 `5601` 端口用于 Kibana 界面的访问；

配置好 `docker-compose.yml` 文件后, 运行 `docker-compose up -d` 便以 daemon 方式启动。用 `docker ps` 命令查看启动状况.

