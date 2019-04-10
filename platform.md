

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


## Running the application

You can now run the application manually:

```bash
TODO

```

or create a service:
```bash
TODO

```
