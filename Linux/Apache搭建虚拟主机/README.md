## Apache 搭建虚拟主机
>此文档以 Mac OX 操作系统为例

### Step 1: 编辑 httpd.conf 文件
在 Apache的配置文件中( httpd.conf ), 开启虚拟主机模块.

```c
# Virtual hosts
Include /private/etc/apache2/extra/httpd-vhosts.conf  #  删除行首的 # , 打开虚拟主机模块.
```

设置目录的权限, 我们把虚拟主机的根目录都放在 /Library/WebServer/ 下, 因此修改一下 /Library/WebServer/ 的权限, 否则访问虚拟主机时会报 403 Forbidden 错误
```c
#
# DocumentRoot: The directory out of which you will serve your
# documents. By default, all requests are taken from this directory, but
# symbolic links and aliases may be used to point to other locations.
#
DocumentRoot "/Library/WebServer/Documents"
<Directory "/Library/WebServer">  # 此处修改路径
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options FollowSymLinks Multiviews
    MultiviewsMatch Any  # 修改后的值
 
    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   AllowOverride FileInfo AuthConfig Limit
    #
    AllowOverride All  # 修改后的值
 
    #
    # Controls who can get stuff from this server.
    #
    Require all granted  # 修改后的值
</Directory>
```
---
### Step 2: 编辑 httpd-vhosts.conf 文件

根据 httpd.conf 文件指定的虚拟主机模块所在路径, 编辑此文件.
Mac OS X 系统 httpd-vhost.conf 文件路径在 /etc/apache2/extra/httpd-vhosts.conf
虚拟主机配置文件中, 默认有个模板. 如果需要建立一个新的虚拟主机, 复制模块, 修改相应的参数即可

```C
# VirtualHost example:
# Almost any Apache directive may go into a VirtualHost container.
# The first VirtualHost section is used for all requests that do not
# match a ServerName or ServerAlias in any <VirtualHost> block.
#
<VirtualHost *:80>  # 虚拟主机 端口号80
    ServerAdmin webmaster@dummy-host.example.com  # 虚拟主机管理员(可选)
    DocumentRoot "/Library/WebServer/Documents"  # 虚拟主机的Apache主目录(必填)
    ServerName localhost  # 虚拟主机名字(必填)
    ServerAlias www.dummy-host.example.com # 虚拟主机别名(可选)
    ErrorLog "/private/var/log/apache2/localhost.example.com-error_log"  # 错误日志路径(可选)
    CustomLog "/private/var/log/apache2/localhost.example.com-access_log" common  # 用户访问日志路径(可选)
</VirtualHost>
```

下面是创建了两个虚拟主机的配置文件
```C
#
# VirtualHost example:
# Almost any Apache directive may go into a VirtualHost container.
# The first VirtualHost section is used for all requests that do not
# match a ServerName or ServerAlias in any <VirtualHost> block.
#
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/Library/WebServer/Documents"
    ServerName localhost
    ServerAlias www.dummy-host.example.com
    ErrorLog "/private/var/log/apache2/localhost.example.com-error_log"
    CustomLog "/private/var/log/apache2/localhost.example.com-access_log" common
</VirtualHost>
 
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/Library/WebServer/News"  # 必填
    ServerName news.local  # 必填
    ErrorLog "/private/var/log/apache2/news.com.example.com-error_log"
    CustomLog "/private/var/log/apache2/news.com.example.com-access_log" common
</VirtualHost>
```

---
### Step 3: 创建虚拟主机的根目录
根据上面虚拟主机的配置, 需要在 `/Library/WebServer/` 下创建 News 目录.

---
### Step 4: 编辑 hosts 文件
在本地如果想访问虚拟主机, 需要修改 `hosts` 文件, 添加相应的 ip-主机名 映射即可.
Mac OS X hosts 文件路径: `/etc/hosts`
```shell
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
127.0.0.1	news.local  # 增加新的域名映射
 
255.255.255.255	broadcasthost
::1             localhost
```
---
### Step 5: 重启 Apache 服务
```shell
Scott-MacBook-Pro:~ scott$ sudo apachectl restart
```


