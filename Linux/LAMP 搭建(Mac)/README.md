## LAMP 搭建(Mac)

### Mac自带Apache配置
* 查看apache版本
```C
sudo apachectl -v
```
* 启动apache
```C
sudo apachectl start
```

* 重启apache
```C
sudo apachectl restart
```

* apache 主配置文件
```C
vim /etc/apache2/httpd.conf
```

* 开启mac内置的php, 默认是关闭的, 打开 `/etc/apache2/httpd.conf ` 文件
```C
// 在169行, 去掉前面的 # 即可.
169 #LoadModule php5_module libexec/apache2/libphp5.so
```

### Mac自带PHP配置
* Mac 系统默认没有 `php.ini` 文件. 在终端下, 进入 `/etc` 目录下, 使用模板生成 `php.ini` 文件.
```C
cp /etc/php.ini.default /etc/php.ini
```

### Mac安装其他版本PHP
终端下一行指令进行安装(可以指定不同的版本)
```C
curl -s https://php-osx.liip.ch/install.sh | bash -s 7.1
```

### Mac下载安装社区版MySQL
1. 下载安装[MySQL](https://dev.mysql.com/downloads/)
2. 在安装MySQL最后一步会提示为 'root'@'localhost' 临时创建的密码, 将此密码记录下来
3. 安装完MySQL之后, 在 `系统偏好设置` 下面会出现一个MySQL图标
4. 确定开启MySQL服务
5. 进入终端修改 MySQL的 root 密码

在终端中进入 MySQL
```C
/usr/local/mysql/bin/mysql -u root -p  /* 回车之后输入 root 的临时密码, 注意密码不会显示出来 */
```

进入MySQL控制台之后, 分别输入下面3条指令
```C
SET PASSWORD = PASSWORD('123456');  /* 设置新密码 */
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;  /* 设置密码永不过期 */
flush privileges;  /* 刷新权限 */
```










