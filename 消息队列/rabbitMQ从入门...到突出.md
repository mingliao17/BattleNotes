# rabbitMQ从入门...到突出
![](../img/internet-person.jpg)

## 环境搭建（centos7）

### 下载erlang和rabbitmq-server安装包
- wget http://www.rabbitmq.com/releases/erlang/erlang-19.0.4-1.el7.centos.x86_64.rpm
- wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.6/rabbitmq-server-3.6.6-1.el7.noarch.rpm

### 安装erlang
- rpm -ivh erlang-19.0.4-1.el7.centos.x86_64.rpm

### 安装rabbitmq

- 安装socat依赖
	- yum install socat -y
- 安装rabbitmq-server
	- rpm -ivh rabbitmq-server-3.6.6-1.el7.noarch.rpm

### 服务相关命令
- /sbin/service rabbitmq-server start --启动服务
- /sbin/service rabbitmq-server stop --关闭服务
- /sbin/service rabbitmq-server status --查看状态

### rabbitmq插件
- rabbitmq-plugins list  --查看插件状态
- rabbitmq-plugins enable rabbitmq_management   --开启web服务插件
  - 访问web管理界面——ip地址:15672
  - guest账号处于安全考虑只能通过localhost登录使用，so，需要建立一个你自己的账号才行
- rabbitmq-plugins disable rabbitmq_management  --禁用web服务插件
### 配置用户
- 新增用户——rabbitmqctl  add_user  用户名  密码
  
  - 例：rabbitmqctl  add_user  mingliao17  password
  
- 删除用户——rabbitmqctl  add_user  mingliao17  password

- 修改用户的密码——rabbitmqctl  change_password  Username  Newpassword

- 查看当前用户列表——rabbitmqctl  list_users

- 设置角色——rabbitmqctl  set_user_tags  用户名  角色名

  - 例：rabbitmqctl  set_user_tags  mingliao17  administrator（没角色应该是登录不了web管理页面）

- 用户角色

  - administrator——超级管理员

    - 可登陆管理控制台(启用management plugin的情况下)，可查看所有的信息，并且可以对用户，策略(policy)进行操作。

  - monitoring——监控者

    - 可登陆管理控制台(启用management plugin的情况下)，同时可以查看rabbitmq节点的相关信息(进程数，内存使用情况，磁盘使用情况等)

  - policymaker——策略定制者

    -   可登陆管理控制台(启用management plugin的情况下), 同时可以对policy进行管理。但无法查看节点的相关信息(上图红框标识的部分)。

      与administrator的对比，administrator能看到这些内容  

  - management——普通管理员

    - 仅可登陆管理控制台(启用management plugin的情况下)，无法看到节点信息，也无法对策略进行管理。

- 用户权限

  - 目前没深入了解，且听下回分解

## java连接rabbitmq

#### 使用框架springboot

。。。且听下回分解

#### 不使用框架

。。。且听下回分解