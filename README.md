#  Install K8s Multy Master Multi Worker in Rocky 9 

##  Hostnames and the IP addresses are given below

<pre>
192.168.122.220  nginx.example.com     nginx
192.168.122.221  master1.example.com     master1
192.168.122.222  master2.example.com     master2
192.168.122.223  master3.example.com     master3
192.168.122.231  worker1.example.com     worker1
192.168.122.232  worker2.example.com     worker2
192.168.122.233  worker3.example.com     worker3
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
yum install nginx

yum install nginx-mod-stream
</pre>
<pre>
   vim /etc/selinux/config
</pre>
<pre>
SELINUX=disable  
</pre>
# add this in nginx.conf
<pre>
stream {
    upstream kubernetes_backend {
        server 192.168.122.221:6443;
        server 192.168.122.222:6443;
        server 192.168.122.223:6443;
    }

    server {
        listen 192.168.122.220:6443;
        proxy_pass kubernetes_backend;
        proxy_connect_timeout 1s;
    }
}
</pre>
# Multi master and worker basic setup 

<pre>
ansible-playbook -i inventory  k8s.yml  
</pre>
  
# master 1 add 
<pre>
  kubeadm init --control-plane-endpoint="192.168.122.220:6443" --upload-certs --apiserver-advertise-address=192.168.122.221 --pod-network-cidr=172.16.0.0/16
</pre>
# If Token Expires then Create a New Token and Replace the Previous One
<pre>
kubeadm token create
</pre>
# Install Calico CNI
<pre>
  kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml
</pre>
# Basic command to check 
<pre>
kubectl get pods -n kube-system
</pre>
# output 
<pre>
  NAME                                                              READY   STATUS    RESTARTS      AGE
calico-kube-controllers-68cdf756d9-shgw7                          1/1     Running   0             21h
calico-node-2vtn8                                                 1/1     Running   0             21h
calico-node-9wzrt                                                 1/1     Running   0             21h
calico-node-bcf4h                                                 1/1     Running   0             21h
calico-node-fnf92                                                 1/1     Running   0             21h
calico-node-g2wt4                                                 1/1     Running   0             21h
calico-node-h8ddr                                                 1/1     Running   0             21h
calico-node-q852h                                                 1/1     Running   0             21h
calico-node-r54qw                                                 1/1     Running   0             21h
calico-node-tlfqq                                                 1/1     Running   0             21h
coredns-5dd5756b68-55dgv                                          1/1     Running   0             22h
coredns-5dd5756b68-xc9fg                                          1/1     Running   0             22h
</pre>
<pre>
  kubectl get nodes -o wide
</pre>
