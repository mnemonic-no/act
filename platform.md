

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
mkdir -p src
cd src
git clone https://github.com/mnemonic-no/act-platform
```

## Build software

For detailed build instructions - consult the [platform documenation](https://github.com/mnemonic-no/act-platform).

```bash
cd ~/src/act-platform
scl enable rh-maven35 "mvn clean install -DskipTests"
```

If the command above fails with references to vulnerability check, you can inspect the possible vulnerability and skip the vulernability tests if they look like false positive. We have also seen that the build must run several times if depdency checks times out.

```bash
cd ~/src/act-platform
scl enable rh-maven35 "mvn clean install -DskipTests -DfailBuildOnAnyVulnerability=false"
```


## Configure the platform

Create a default configuration under ~/conf:

```bash
cd ~/src/act-platform
mkdir -p ~/conf
cp executable/*.localhost ~/conf
cp -r executable/examples ~/conf/trigger
sed -Ei "s/^(access.controller.properties.file=).*/\1$(pwd | sed 's/\//\\\//g')\/acl.properties.localhost/" application.properties.localhost
sed -Ei "s/^(trigger.administration.service.configuration.directory=).*/\1$(pwd | sed 's/\//\\\//g')\/trigger/" application.properties.localhost
```

Create cassandra keyspace
```bash
cqlsh -f ~/src/act-platform/executable/testsrc/resources/setup.cql
```

## Running the application

You can now run the application manually:

```bash
java -Dapplication.properties.file=$HOME/conf/application.properties.localhost -jar $HOME/src/act-platform/executable/target/act-platform-0.0.1-SNAPSHOT.jar guice module=no.mnemonic.act.platform.rest.modules.TiRestModule module=no.mnemonic.act.platform.service.modules.TiServiceModule
```

You will find logs under logs.

## Install as a service

Most likely you would like to install it as a service.

This must be performed as the `root` user or with sudo and must point to the user which was used to build the software.

```bash
echo "[Unit]
Description=ACT service
After=elasticsearch.service

[Service]
Environment=EXECUTABLE=/home/act/src/act-platform/executable/target/act-platform-0.0.1-SNAPSHOT.jar
Environment=PROPERTIES=/home/act/conf/application.properties.localhost
Environment='ARGS=guice module=no.mnemonic.act.platform.rest.modules.TiRestModule   module=no.mnemonic.act.platform.service.modules.TiServiceModule'
Environment=JAVA_OPTS=-XX:-OmitStackTraceInFastThrow
User=act
WorkingDirectory=/home/act
ExecStart=/usr/bin/java \${JAVA_OPTS} -Dapplication.properties.file=\${PROPERTIES} -jar \${EXECUTABLE} \"\$ARGS\"
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=act
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target " > /etc/systemd/system/act-app.service

systemctl daemon-reload
systemctl enable act-app.service
systemctl start act-app

```
