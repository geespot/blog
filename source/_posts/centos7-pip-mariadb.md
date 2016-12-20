---
title: centos7_pip_mariadb
date: 2016-12-09 14:32:03
tags:
- 技术
- 运维
categories: [linux,数据库,运维]
---

### centos7 yum安装maridb:
```bash
$  yum -y install mariadb*
$  systemctl start mariadb.service
$  systemctl enable mariadb.service
```   

### centos7 pip安装：
```bash
$  sudo yum -y install epel-release
$  sudo yum -y install python-pip
$  yum clean all
```   

###  firewall-cmd 端口 开启
```bash
$   firewall-cmd --zone=public --add-port=80/tcp --permanent
$   firewall-cmd --zone=public --add-port=81/tcp --permanent
$   firewall-cmd  --reload
$   firewall-cmd --zone=public --list-ports
```   

### mysql数据库导出导入
备份：
```sql
mysqldump  -h 127.0.0.1 -uroot -p --default-character-set=utf8 pycmdb > pycmdb.sql
```   
恢复：
```sql
mysql -uroot -p
source  pycmdb.sql
TRUNCATE table assets_ip;
```   

###  PIL/Pillow IOError: decoder jpeg not available
```bash
pip uninstall Pillow or pip uninstall PIL
sudo yum install libjpeg-devel
pip install Pillow
```   