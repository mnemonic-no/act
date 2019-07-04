
# ACT Image

## About the image

This image is a virtual appliance that can be installed in virtual box or vmware. The image contains a "clean" installation, with only the ACT data model. When booting the image it will start to bootstrap the image with feeds, workers, enrichment and optionally a repository of reports.

## Download

The image is ~ 19GB and can be downloaded here:

* [https://act-image.s3-eu-west-1.amazonaws.com/act-image-2019-07-03.ova](https://act-image.s3-eu-west-1.amazonaws.com/act-image-2019-07-03.ova)

## Image content

The image contains:

* [Centos](https://www.centos.org/)
* [Apache Cassandra](http://cassandra.apache.org/)
* [Elasticsearch](https://www.elastic.co/)
* [Apache NiFI](https://nifi.apache.org/)
* [ACT Platform](https://github.com/mnemonic-no/act-platform)
* [ACT Workers](https://github.com/mnemonic-no/act-workers)
* [ACT SCIO](https://github.com/mnemonic-no/act-scio)
* [ACT SCIO API](https://github.com/mnemonic-no/act-scio-api)
* [ACT Datamodel](https://github.com/mnemonic-no/act-types)
* [ACT Frontend](https://github.com/mnemonic-no/act-frontend)

## Requirements

The minimum requirements for this image are:

* Tested on Virtual Box 6, but should also work on VMWare
* 10GB RAM
* 4 CPUs
* 60 GB disk

# Usage

## Exposed services

* SSH: localhost:2222
* Web Frontend: [http://localhost:8888](http://localhost:8888)
* NiFi (worker orchestration): [http://localhost:8888/workers/nifi](http://localhost:8888/workers/nifi)
* Swagger documentation: [http://localhost:8888/swagger/](http://localhost:8888/swagger/)
* SCIO API: [http://localhost:8888/scio](http://localhost:8888/scio)

# Configuration

## OS

### Login
SSH is port forwarded on port 2222 on localhost.

* user: root
* password: actS3cur3

Change the password and add a personal account after installation.

## SCIO Feeds

```bash
$ crontab -u act -l
# Feeds
38 * * * * /opt/scio_feeds/run.sh

# Report repositories
# 55 * * * * /opt/auto_report_download/run.sh
```

## ACT workers

TODO

## SCIO feeds

TODO

## nginx

TODO

# Troubleshooting

## Logs

Relevant logs are available here:

* ACT Platform: /home/act/logs
* Workers: /home/nifi/workerlogs
* SCIO: syslog

## Nifi provenance

TODO
