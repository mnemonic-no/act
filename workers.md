# Workers

You can now install some [workers](https://github.com/mnemonic-no/act-workers) and administrative tools for getting facts in to the platform.

```bash
pip3 install act-workers act-admin
```

## Configuration
Workers can be configured using environment variables, command line arguments and a configuration file. In this example we will use an ini file to create a configuration file that will be used for all workers.

First, run `act-worker-config`, which is part of the python package installed above. This should not be ran as `root`, but as the user that should run the workers, e.g. `act`.

```bash
act-worker-config user
```

This will create a config file as `~/.config/act/act.ini`.

As default, all options are commented out, but the defaults will be shown, e.g.:

```
# loglevel = info
```

Please not that you will most often not add act-baseurl and user-id to the configration as this will not make it possible to test workers on the command line and printing facts to stdout.

## Common options

All workers have some common options, and some of the most important one to start of with are shown below. Use `-h` on a worker to show all options.

* `--act-baseurl and --user-id` - if these are specified facts will be added to the platform. If they are not provided, a representation of the fact will be sent to stdout instead. You will often omit these when testing out for the first time.
* `--output-format` - Format of facts sent to stdout. Default is json, which can be used to "route facts" in NiFi or other platforms. When debugging/testing you can also use "--output-format str" which will show facts in a human readable format.
* `--proxyy-string` - Proxy string for external queryies (on the format `http://<HOST>:<PORT>`.

## Origins

All facts added to the platform will have an origin, to see where they come from. These can be changed but you can use the defaults as provded by `origin-name` in the configuration file in ~/.config/act/act.ini.

These origins must also be added to the platform. To add all origins specified in `act.ini` run:

```bash
act-origin --act-baseurl http://localhost:8888 --user-id 1 --from-config
```

act-origin is installed in the act-admin tool with pip.

## Access Mode

Facts in the platform also have access modes which are relevant if you later share facts from the platform. Defaults are included in `act.ini`, but you can change them as you like.


## Input workers
Some workers are `input workers` that creates facts from external sources of structured data. Some examples are:

* `act-attack` - Creates facts from Mitre Att&ck
* `act-country-regions` - Creates facts from ISO-3166 (countries and regions)
* `act-misp-feeds` - Creates facts MISP feeds

To test one of the workers, execute the following (add --proxy-string if you are behind a proxy):

```
act-country-regions --output-format str
```

This will output 250+ facts, including these:

```
(country/Afghanistan) -[memberOf]-> (subRegion/Southern Asia)
(subRegion/Southern Asia) -[memberOf]-> (region/Asia)
(country/Åland Islands) -[memberOf]-> (subRegion/Northern Europe)
(subRegion/Northern Europe) -[memberOf]-> (region/Europe)
(country/Albania) -[memberOf]-> (subRegion/Southern Europe)
(...)
```

To add all these facts to the platform, run with `--act-baseurl` and `--user-id`:
```
act-country-regions --act-baseurl http://localhost:8888 --user-id 1
```

You will mosst likely also add data from Mitre Att&ck:
```
act-attack --act-baseurl http://localhost:8888 --user-id 1
```

## Enrichment workers

Other workers are used for enrichment of data. Typically they would need an input, like an IPv4 address, to be used as query parameter.

* `act-shadowserver-asn` - Lookups ASN information for an IP address. Requires outbound access to UDP/43 towards asn.shadowserver.org
* `act-mnemonic-pdns` - Lookups IPv4/FQDN on mnemonic pDNS. This works without a API key for a low volume of queries).
* `act-vt` - Lookups IPv4/FQDN/hash on Virus Total (requires API key at Virus Total)

These workers read from stdin and queries the the external resource for any relevant information. Some examples:

```
echo -n www.mnemonic.no | act-mnemonic-pdns --output-format str
```

We will now show the usage of the act-shadowserver-asn worker. This worker needs a local copy of [this file](https://raw.githubusercontent.com/lukes/ISO-3166-Countries-with-Regional-Codes/master/all/all.json), which it will use to translate country codes to country names.

```bash
mkdir -p .local/share/actworkers/
curl -o .local/share/actworkers/country-regions.json https://raw.githubusercontent.com/lukes/ISO-3166-Countries-with-Regional-Codes/master/all/all.json
```

Then we must update the configuration file (~/.config/actworkers/actworkers.ini) to point to this file:

```
[shadowserver-asn]
country-codes = /home/act/.local/share/actworkers/country-regions.json
```

Now you can run the worker like this:


```bash
echo -n 94.127.56.170  | act-shadowserver-asn  --stdin --output-format str
```

Which will give you an output similar to this:
```
[act@ip-172-31-32-80 ~]$ echo -n 94.127.56.170  | act-shadowserver-asn  --stdin --output-format str
[2019-05-03 07:59:22] app=shadowserver-asn level=INFO msg=Result from cache: ASNRecord(asn=48469, prefix='94.127.56.0/22', asname='MNEMONIC', cn='NO', isp='OSL Mnemonic as, Oslo, Norway, NO', peers=['174', '3292'])
(ipv4/94.127.56.170) -[memberOf/ipv4Network]-> (ipv4Network/94.127.56.0/22)
(ipv4Network/94.127.56.0/22) -[memberOf/asn]-> (asn/48469)
(asn/48469) -[name/MNEMONIC]
(organization/osl mnemonic as) -[owns/asn]-> (asn/48469)
(organization/osl mnemonic as) -[locatedIn]-> (country/Norway)
```
