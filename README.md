# Semi-Automated Cyber Threat Intelligence - ACT

[*Semi-Automated Cyber Threat Intelligence*](https://www.mnemonic.no/research-and-development/semi-automated-cyber-threat-intelligence/) (ACT) is a research project led by mnemonic as with contributions from the University of Oslo, NTNU, Telenor, Norwegian Security Authority (NSM), KraftCERT and Nordic Financial CERT.

The main objective of the ACT project is to develop a platform for cyber threat intelligence to uncover cyber attacks, cyber espionage and sabotage.
The project will result in new methods for data enrichment and data analysis to enable identification of threat agents, their motives, resources and attack methodologies.

In addition, the project will develop new methods, work processes and mechanisms for creating and distributing threat intelligence and countermeasures to stop ongoing and prevent future attacks.

The code of the ACT platform is published under an Open Source license (ISC).

This repository contains documentation on how to install the ACT platform, including platform, frontend, datamodel and tools for feeding the platform with structured data.

The guide assumes you are running CentOS 7.x, but other linux distributions will most likely be supported if they have all the necessary requirements.

## Testing the platform

An open, read only, instance is available on [https://act.mnemonic.no](https://act.mnemonic.no), so if you want to try out the platform without setting it up yourself you can go there.

<!--- ## Architecture -->

## Detailed installation guide

* [Requirements](requirements.md)
* [Core platform](platform.md)
* [Data model](datamodel.md)
* [GUI](frontend.md)
* [Workers](workers.md)
* [Scio](scio.md)
* [NiFi](nifi.md)

## Virtual Appliance

* [Download and usage](virtual-appliance.md)

<!--- ## Usage -->

<!--- ## Road map -->

<!--- ## FAQ -->
