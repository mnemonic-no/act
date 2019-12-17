# Scio API

## Installation

### Clone repository

As the `act` user:

```bash
cd $HOME/src
git clone https://github.com/mnemonic-no/act-scio-api
cd act-scio-api
```

### Build

```bash
lein deps
lein uberjar
```

### Copy config files / software to /opt/scio

As `root`:

```bash
mkdir -p /opt/scio
chown act:act /opt/scio
```

Now, as user `act`, copy required files to /opt/scio:

```bash
cd $HOME/src/act-scio
cp target/scio-api-*standalone.jar examples/systemd/upgrade-latest-scio-api.sh /opt/scio
```

As `root`:

```bash
cp /home/act/src/act-scio-api/examples/systemd/scio-api.service /usr/lib/systemd/system
sed -iE 's/User=scio/User=act/' /usr/lib/systemd/system/scio-api.service
systemctl enable scio-api.service
/opt/scio/upgrade-latest-scio-api.sh
```

This should install the scio-api service is and have it up and running.
