# Requirements

This guide assume everything will be running on one host, The minimum hardware requirments will then be:

* 50GB Disk
* 8 CPU
* 32GB Ram

If you plan to ingest a high volume of data or run complex queries it is advised to setup dedicated clusters for Cassandra and Elasticsearch.

## Commands
This guide includes a step by step guide to install all requirements and software components on CentOS 7.x. Unless otherwise stated, the commands should be executed as root. 
If installing on another platform, install Cassandra, Elasticsearch, Java JDK (11), Node (yarn) and python 3.

## RPM Packages

We add both epel and scl as repositires (this will accept the GPG key from SCL).

```bash
yum -y install deltarpm epel-release centos-release-scl nginx jq vim java-1.8.0-openjdk python36 python36-pip git beanstalkd unzip

yum-config-manager --enable rhel-server-rhscl-7-rpms
yum install -y rh-maven35
```

Enable beanstalk:
```bash
systemctl enable beanstalkd
service beanstalkd start
```
## SELINUX

Some the installation procedures are known to fail with SELINUX enabled. For an internal test setup, you can disable it:

```
sed -Ei "s/(SELINUX=).*/\1disabled/" /etc/sysconfig/selinux
setenforce 0
```

If you would like to have SELINUX enabled, here are some configuration to get you started:

```
# NGINX
setsebool -P httpd_can_network_connect 1
setsebool -P httpd_read_user_content 1
chcon -Rt httpd_sys_content_t /usr/share/nginx/html
```

## Add user

Install a user that will be used to check out and build the sofwtare:

```bash
adduser act
```

## Cassandra

A running installation of [Apache Cassandra](https://cassandra.apache.org/). Any version of Apache Cassandra 3.x is required.

```bash
echo '[cassandra]
name=Apache Cassandra
baseurl=https://www.apache.org/dist/cassandra/redhat/311x/
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://www.apache.org/dist/cassandra/KEYS' > /etc/yum.repos.d/cassandra.repo
yum -y install cassandra
```

Start cassandra and make sure it starts at boot time.
```bash
service cassandra start
chkconfig cassandra on
```

## Elasticsearch
A running installation of [Elasticsearch](https://www.elastic.co/products/elasticsearch). Version 7.16+ of Elasticsearch is required.

```bash
echo '
[elasticsearch]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md' > /etc/yum.repos.d/elasticsearch.repo
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
yum install -y elasticsearch kibana

systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl enable kibana.service
service elasticsearch start
sleep 5
service kibana start
```

Use zero replicas, since we only have a single node:

```bash
curl -H 'Content-Type: application/json' -XPUT localhost:9200/_template/zeroreplicas -d ' {
    "template" : "*",
    "settings" : {
        "number_of_replicas" : 0
    }
}'
```

Use 8GB of RAM for elasticsearch:

```bash
cp /etc/elasticsearch/jvm.options /etc/elasticsearch/jvm.options.bak
sed -iE 's/^-Xms[0-9]\+g$/-Xms8g/' /etc/elasticsearch/jvm.options
sed -iE 's/^-Xmx[0-9]\+g$/-Xmx8g/' /etc/elasticsearch/jvm.options
service elasticsearch restart
```

## node and yarn

The frontend requires nodejs and yarn to build. Follow these steps to install (note - this will install and accept the RPM keys automatically).

```bash

rpm -ivh https://rpm.nodesource.com/pub_11.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm
yum-config-manager --enable nodesource

yum install -y nodejs

echo '
[yarn]
name=Yarn Repository
baseurl=https://dl.yarnpkg.com/rpm/
enabled=1
gpgcheck=1
gpgkey=https://dl.yarnpkg.com/rpm/pubkey.gpg
' > /etc/yum.repos.d/yarn.repo
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch

yum install -y yarn
```

Also make sure ~/bin is in the `act` users $PATH.

