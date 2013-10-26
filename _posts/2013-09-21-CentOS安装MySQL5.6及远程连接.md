---
layout: post
title:  "CentOS安装MySQL5.6及远程连接"
---

  首先从mysql官网下载MySQL Community Server，在Select Platform处选择Source Code，然后选择Generic Linux(Architecture Independent)，Compressed TAR Archive。
  
  在MySQL 5.6中，所有平台都使用了CMAKE来进行编译，在安装MySQL之前，要先安装好gcc等依赖包和CMAKE。
  
  安装gcc和缺少的依赖包：
  
{% highlight ruby %}
sudo yum install gcc-c++
sudo yum isntall ncurses-devel
{% endhighlight %}

然后安装CMAKE：

{% highlight ruby %}
sudo yum install cmake cmake-gui
{% endhighlight %}

准备工作完成后，在shell中向系统新增用户组和用户“mysql”：

{% highlight ruby %}
sudo groupadd mysql
sudo useradd -r -g mysql mysql
{% endhighlight %}

现在进入mysql-VERSION.tar.gz所在目录，编译源码，完成安装：

{% highlight ruby %}
tar zxvf mysql-VERSION.tar.gz
cd mysql-VERSION
cmake .
make
sudo make install
{% endhighlight %}

安装完成后，进行初始化设置：

{% highlight ruby %}
cd /usr/local/mysql
sudo chown -R mysql .
sudo chgrp -R mysql .
sudo scripts/mysql_install_db --user=mysql
sudo chown -R root .
sudo chown -R mysql data
{% endhighlight %}

接着运行mysql服务：

{% highlight ruby %}
sudo bin/mysqld_safe --user=mysql &
{% endhighlight %}

测试服务是否启动成功：

{% highlight ruby %}
sudo bin/mysqladmin version
{% endhighlight %}

若出现mysqld_safe starting mysqld daemon with databases from /var/lib/mysql和[ERROR]Cant't open the mysql.plugin table的错误信息，修改mysql配置文件：

{% highlight ruby %}
sudo vi /usr/local/mysql/my.cnf
{% endhighlight %}

加入下面两行：
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data

重新启动mysql，再运行测试命令，若正确打印出版本信息，则表示启动成功，关闭mysql服务的命令是：

{% highlight ruby %}
sudo bin/mysqladmin -u root shutdown
{% endhighlight %}

MySQL服务正常启动后，最好对mysql默认用户设置密码，现在先启动mysql：

{% highlight ruby %}
sudo bin/mysql -u root
{% endhighlight %}

为root用户和匿名用户设置密码：

{% highlight ruby %}
UPDATE mysql.user SET PASSWORD=PASSWORD('yourpassword')
  WHERE USER='root';
UPDATE mysql.user SET PASSWORD=PASSWORD('yourpassword')
  WHERE USER='';
FLUSH PRIVILEGES;
{% endhighlight %}

现在新增一个用户：

{% highlight ruby %}
GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost'
  IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'test'@'yourip' IDENTIFIED
  BY 'yourpassword' WITH GRANT OPTION;
{% endhighlight %}

虚拟机上的网络模式用的是NAT，这里的“yourip”是宿主机上，运行“ifconfig”后,vmnet8中inet后的ip值。
现在打开虚拟机linux系统的3306端口：

{% highlight ruby %}
sudo iptables -I INPUT -p tcp -m state --state NEW -m tcp
  --dport 3306 -j ACCEPT
{% endhighlight %}

设置完成后，在宿主机上运行NAVICAT，使用刚才新增的用户名，密码及虚拟机IP配置连接信息，测试连接，连接成功后即可在宿主机上操作虚拟机中的MySQL。
