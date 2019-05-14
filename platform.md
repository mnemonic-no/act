# Requirements

To install the core platform you will need to have an installation of Cassandra, Elasticsearch, and all software specified by the RPMs in [requirements](requirements.md).

## su

change to a non privilegded user.

```bash
su - act
```


## Checkout software

Checkout software from git to src/act-platform.

```bash

mkdir -p ~/src
cd ~/src
git clone https://github.com/mnemonic-no/act-platform
```

## Build software

For detailed build instructions - consult the [platform documentation](https://github.com/mnemonic-no/act-platform).

```bash
cd ~/src/act-platform
scl enable rh-maven35 "mvn clean install -DskipSlowTests"
```

If the command above fails with references to vulnerability check, you can inspect the possible vulnerability and skip the vulernability tests if they look like false positive. We have also seen that the build must run several times if depdency checks times out.

```bash
cd ~/src/act-platform
scl enable rh-maven35 "mvn clean install -DskipSlowTests -DfailBuildOnAnyVulnerability=false"
```


## Configure the platform

Create a default configuration under ~/conf:

```bash
cd ~/
mkdir act-platform
cd ~/act-platform
tar zxvf ~/src/act-platform/deployment-combined/target/act-platform-deployment-combined-0.0.1-SNAPSHOT.tar.gz

```

Create cassandra keyspace
```bash
cqlsh -f ~/src/act-platform/dao/testsrc/resources/setup.cql
```

## Running the application

You can now run the application manually:

```bash
cd ~/
export ACT_PLATFORM_CONFDIR=$HOME/conf
export ACT_PLATFORM_LOGDIR=$HOME/logs
act-platform/init.sh start
```

This will create the initial config under ~/conf. You will afterwars need to fix the paths for the `access.controller.properties.file` and `trigger.administration.service.configuration.director`:

```bash
sed -Ei 's/=conf\/(.*)/=\/home\/act\/conf\/\1/' $HOME/conf/application.properties
```

After this you can stop/start again:

```bash
cd ~/
export ACT_PLATFORM_CONFDIR=$HOME/conf
export ACT_PLATFORM_LOGDIR=$HOME/logs
act-platform/bin/init.sh start
```


## Install as a service

Most likely you would like to install it as a service.

This must be performed as the `root` user or with sudo and must point to the user which was used to build the software.

```bash
echo "[Unit]
Description=ACT service
After=elasticsearch.service

[Service]
Type=oneshot
Environment=ACT_PLATFORM_CONFDIR=/home/act/conf
Environment=ACT_PLATFORM_LOGDIR=/home/act/logs
User=act
WorkingDirectory=/home/act
ExecStart=/home/act/act-platform/bin/init.sh start
ExecStop=/home/act/act-platform/bin/init.sh stop
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=act
RemainAfterExit=yes
KillMode=none
RestartSec=5

[Install]
WantedBy=multi-user.target " > /etc/systemd/system/act-app.service

systemctl daemon-reload
systemctl enable act-app.service
systemctl start act-app

```
