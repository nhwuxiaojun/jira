# centos环境下docker搭建jira


## 1. 万能 jar 

在当前目录下atlassian-extras-3.2.jar




## 2. pull docker 镜像：  jira:7.12.1(目前的最新版本)    mysql:5.7

```
docker pull cptactionhank/atlassian-jira-software
docker pull mysql:5.7
```

## 3. 启动mysql docker实例
docker run --name atlassian-mysql --restart always -p 3306:3306 -v /opt/mysql_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=passwd -d mysql:5.7 

#记得修改passwd


## 4. 数据库设置
1. 连接mysql
```
docker run -it --link atlassian-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

2. 创建jira数据库,并添加jira用户
```
create database jira default character set utf8 collate utf8_bin;
CREATE USER `jira`@`%` IDENTIFIED BY 'jira';GRANT ALL ON *.* TO `jira`@`%` WITH GRANT OPTION;
alter user 'jira'@'%' identified with mysql_native_password by 'jira';
```

3. 修改mysql事物隔离级别
```
set global transaction isolation level read committed;
set session transaction isolation level read committed;
```


## 5. 启动jira实例

```
docker run --detach --restart always -v /data/atlassian/confluence:/home --link atlassian-mysql:mysql --publish 8088:8080 cptactionhank/atlassian-jira-software
```

##   6. Jira配置数据连接，建表

在Database setup，选择My Own Database，按以下信息选择和输入
注意：因为在容器里，不能填写localhost，只能填写IP

```
Database Type：MySQL
Hostname：192.168.0.4
Port：3306
Database：jira
Username：jira
Password：jira
```



## 7.配置LDAP认证



![](D:\迅雷下载\下载.png)



## 8. 设置完成后，可以通过LDAP用登录。



![1539501660891](C:\Users\xiaojun.wu\AppData\Local\Temp\1539501660891.png)
