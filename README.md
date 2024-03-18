#  Install K8s Multy Master Multi Worker in Rocky 9 

##  Hostnames and the IP addresses are given below

<pre>
192.168.122.20  nginx.example.com     nginx
192.168.122.21  master1.example.com     master1
192.168.122.22  master2.example.com     master2
192.168.122.23  master3.example.com     master3
192.168.122.31  worker1.example.com     worker1
192.168.122.32  worker2.example.com     worker2
192.168.122.33  worker3.example.com     worker3
</pre>
##  Configure  nginx Server
<pre>
  yum -y install vim wget man tcpdump bash-completion net-tools yum-utils bind-utils sysstat dstat  lsof epel-release firewalld mlocate && yum -y install iftop htop nload telnet && yum -y update
</pre>
#### Install the prerequisites
<pre>
  sudo yum install yum-utils
</pre>
<pre>
  vim /etc/yum.repos.d/nginx.repo
</pre>
<pre>
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
</pre>
<pre>
  sudo yum-config-manager --enable nginx-mainline
</pre>
<pre>
  sudo yum install nginx
</pre>
