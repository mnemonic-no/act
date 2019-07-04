
# ACT Image

## About the image

This image is a virtual appliance that can be installed in virtual box or vmware. The image is contains a "clean" installation, with only the ACT data model. When booting the image it will start to bootstrap the image with feeds, workers, and optionally repositories of reports.

## Download

The image is ~ 19GB and can be downloaded here:

* [https://act-image.s3-eu-west-1.amazonaws.com/act-image-2019-07-03.ova](https://act-image.s3-eu-west-1.amazonaws.com/act-image-2019-07-03.ova).

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



# Configuration

## Login

## ACT workers

## SCIO feeds

# Troubleshooting

## Logs
