# Scio

## Installation

### Clone repository

As the `act` user:

```bash
cd $HOME/src
git clone https://github.com/mnemonic-no/act-scio.git
cd act-scio
```

### Download vendor files to vendor/ (OpenNLP models, Geo names and TLDs)

This will populate the vendor/ directory.

```bash
scripts/get-vendor-files.sh
```

### Build

```bash
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
mkdir -p /opt/scio/documents
cp -r etc vendor target/uberjar/scio-back-*-SNAPSHOT-standalone.jar examples/systemd/upgrade-latest.sh /opt/scio
```

As `root`:

```bash
cp /home/act/src/act-scio/examples/systemd/scio-back.service /usr/lib/systemd/system
sed -iE 's/User=scio/User=act/ /usr/lib/systemd/system/scio-back.service
systemctl enable scio-back.service
/opt/scio/upgrade-latest.sh
```

This should install the scio-back service is and have it up and running.
