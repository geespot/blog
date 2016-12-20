---
title: packetbeat
date: 2016-12-07 15:50:18
tags:
- 技术
categories: [linux,数据库,开发]
---

### 安装：
[安装方法地址1.3](https://www.elastic.co/guide/en/beats/packetbeat/1.3/packetbeat-installation.html)
```bash
$  sudo yum install libpcap
$  curl -L -O https://download.elastic.co/beats/packetbeat/packetbeat-1.3.1-x86_64.rpm
$  sudo rpm -vi packetbeat-1.3.1-x86_64.rpm
```   
### 配置：
* 配置文件 vim /etc/packetbeat/packetbeat.yml

```bash
  mysql:
    # Configure the ports where to listen for MySQL traffic. You can disable
    # the MySQL protocol by commenting out the list of ports.
    ports: [3306]

output:

  ### Elasticsearch as output
  elasticsearch:
    # Array of hosts to connect to.
    # Scheme and port can be left out and will be set to the default (http and 9200)
    # In case you specify and additional path, the scheme is required: http://localhost:9200/path
    # IPv6 addresses should always be defined as: https://[2001:db8::1]:9200
    hosts: ["10.57.117.104:9200","10.57.112.126:9200"]
```   
