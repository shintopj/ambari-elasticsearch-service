# Ambari Elasticsearch Service

The Ambari Elasticsearch Service is a custom service for Ambari that allows you to install and manage Elasticsearch via Ambari.  This service is provided as a community project and is not supported by Hortonworks.  Futhermore, this service is intended for testing and development and should not be used in a production environment.  This service is for Ambari 2.4.x and Elasticsearch 5.x.

## System Requirements

Prior to deploying the Ambari Elasticsearch Service, there are specific operating system configuration changes needed for Elasticsearch to run properly.

When configuring Elasticsearch to bind to a non-loopback address, Elasticsearch will perform additional system checks called "bootstrap checks".  If these bootstrap checks fail, Elasticsearch will shutdown.  You can read more about those checks here: <https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html>

### /etc/security/limits.conf

Modify */etc/security/limits.conf* to include the following settings:

```
elasticsearch    -       nofile         65536
elasticsearch    -       nproc          2048
elasticsearch    -       memlock        unlimited
```

NOTE: This assumes that elasticsearch is the user configured to run Elasticsearch via the Ambari configuration.

### /etc/sysctl.conf

Modify */etc/sysctl.conf* to include the following settings:

```
# Controls mmap counts
vm.max_map_count = 262144
```

You can read more about Elasticsearch configuration settings here: <https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html>

These changes should be made to any node on which you plan to deploy Elasticsearch.  After the changes have been implemented, it is a good idea to reboot the servers to ensure the changes take affect.

## Installation

To install this service, you need access to the Ambari Server with sudo permissions.

```
VERSION=`hdp-select status hadoop-client | sed 's/hadoop-client - \([0-9]\.[0-9]\).*/\1/'`
sudo git clone https://github.com/shintopj/ambari-elasticsearch-service /var/lib/ambari-server/resources/stacks/HDP/$VERSION/services/ELASTICSEARCH
```

If you do not have the ability to use git, you can download the repo archive and extract it to directory shown above.

After you have installed the service, you need to restart the Ambari Server.

```
sudo service ambari-server restart
```

Once the Ambari Server service has been restarted, you should see Elasticsearch as an available service to install from the Add Service screen.


If Ambari fail to start the service with the following error 

resource_management.core.exceptions.ExecutionFailed: Execution of '/opt/elasticsearch/bin/elasticsearch-plugin remove x-pack' returned 78. -> removing [x-pack]...
ERROR: plugin [x-pack] not found; run 'elasticsearch-plugin list' to get list of installed plugins

then try the below steps to fix

```
cd /opt/elasticsearch
bin/elasticsearch-plugin install x-pack
```

## Compatibility

This service has been tested with the following:

- Ubuntu 16.04.3 LTS
- Ambari 2.5.2.0
- HDP HDP-2.6.2.0
- Elasticsearch 6.2.2

## Limitations

The following limitations currently apply:

- The service currently deploys all nodes as master=true, data=true, and ingest=true.
- The service has only been tested with CentOS/RHEL 6.x.
- The service exposes only the default configuration options present in the Elasticsearch configuration file.
- Quick Links do not currently work.
- The service does not currently support Kerberos.
- The service does not currently have Ambari Service Advisor or Ambari Alert functionality.

## Contributors

This project is a modified to enable support for Elasticsearch 6.2.2  However, the base started from a fork of <https://github.com/Symantec/ambari-elasticsearch-service>

This project is a complete refactoring to enable support for Elasticsearch 5.x.  However, the base started from a fork of <https://github.com/Symantec/ambari-elasticsearch-service>

## License

Licensed under the Apache License, Version 2.0 (the “License”); you may not use this file except in compliance with the License.

You may obtain a copy of the License at

<http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an “AS IS” BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
