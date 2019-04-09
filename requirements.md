# Requirements

This guide assume everything will be running on one host. The minimum hardware requirments will then be:

* 50GB Disk
* 8 CPU
* 32GB Ram

If you plan to ingest a high volume of data or run complex queries it is advised to setup dedicated clusters for Cassandra and Elasticsearch.

## Commands
This guide includes a step by step guide to install all requirements and software components. Unless otherwise stated, the commands should be executed as root.

## RPM Packages

```bash
yum -y install deltarpm epel-release nginx jq vim java-1.8.0-openjdk python36 python36-pip maven
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
service cassandra start
```


## Elasticsearch
A running installation of [Elasticsearch](https://www.elastic.co/products/elasticsearch). Version 6.6+ of Elasticsearch is required.

```bash
echo '
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
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

TODO: setup for "single node", memory, etc


## node and yarn

The frontend requires nodejs and yarn to build. Follow these steps to install

```

```bash
```
