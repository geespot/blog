---
title: rabbitmq_install
date: 2016-12-19 16:55:38
tags:
 - 技术
categories: [[linux,数据库,运维]]
---

### 一、安装：
```bash
rpm -Uvh http://download.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-8.noarch.rpm
yum clean all
yum install erlang
rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
 yum install rabbitmq-server-3.6.6-1.noarch.rpm
yum list |grep rabbit
yum install rabbitmq-server.noarch 
systemctl enable rabbitmq-server
systemctl start rabbitmq-server
systemctl status rabbitmq-server

```   

### 二、配置 添加user和vhost：
```bash
rabbitmqctl add_vhost apis
rabbitmqctl add_vhost search
rabbitmqctl add_user shgt password
rabbitmqctl  set_permissions  -p  apis  shgt  ConfP  WriteP  ReadP
rabbitmqctl  set_permissions -p apis  shgt '.*' '.*' '.*'
rabbitmqctl  set_permissions -p search  shgt '.*' '.*' '.*'
rabbitmqctl list_user_permissions shgt

```   

### 三、设置 web管理帐号：
```bash
/usr/lib/rabbitmq/bin/rabbitmq-plugins enable rabbitmq_management 
systemctl stop rabbitmq-server
systemctl start rabbitmq-server
systemctl status rabbitmq-server
rabbitmqctl  set_user_tags  shgt  monitoring
rabbitmqctl list_user_permissions shgt
rabbitmqctl list_permissions -p apis
rabbitmqctl add_user jobs password
rabbitmqctl  set_user_tags  jobs  monitoring

```   
