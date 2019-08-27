# kafka-elk-docker-compose
This repository deploys with *docker-compose* an ELK stack which has kafka cluster buffering the logs collection process. This repository tries to make your life easier while testing a similar architecture. It is highly discouraged to use this repository as a production ready solution of this stack.

## Setup

1.  [Install Docker engine](https://docs.docker.com/engine/installation/)
2.  [Install Docker compose](https://docs.docker.com/compose/install/)
3.  Clone this repository:
    ```
    git clone git@github.com:vigneshmahalingam/kelk-grafana.git
    ```
4. [Configure File Descriptors and MMap](https://www.elastic.co/guide/en/elasticsearch/guide/current/_file_descriptors_and_mmap.html)
Please note that this works only for linux
To do so you have to type the following command:
    ```
    sysctl -w vm.max_map_count=262144
    ```
    Be aware that the previous sysctl setting vanishes when your machine restarts.
    If you want to make it permanent place `vm.max_map_count` setting in your `/etc/sysctl.conf`.

    macOS with Docker for Mac
    The vm.max_map_count setting must be set within the xhyve virtual machine:

    ```
    $ screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
    ```
    
    Just press enter and configure the sysctl setting as you would for Linux:
    ```
    sysctl -w vm.max_map_count=262144
    ```
    
5. Create the elasticsearch volume:
    ```bash
    $ cd kafka-elk-docker-compose
    $ mkdir esdata01
    $ mkdir esdata02
    $ mkdir grafanadata
    ```
    By default the *docker-compose.yml* uses *esdata* as the host volumen path name. If you want to use another name you have to edit the *docker-compose.yml* file and create your own structure.
6. Create the *apache-logs* folder:
    ```bash
    $ cd kafka-elk-docker-compose
    $ mkdir apache-logs
    ```
    This repository uses a default *apache container* to generate logs and it is required for *filebeat* to be present. If you do not want to use this apache or you want to add new components to the system just use the *docker-compose.yml* as a base for your use case.

## Usage

Deploy your Kafka+ELK Stack using *docker-compose*:

```bash
$ export DOCKER_KAFKA_HOST=$(ipconfig getifaddr en0)
$ docker-compose up -d
```
By default the apache container generating logs is exposed through port 8888. You can perform some requests to generate a few log entries for later visualization in kibana:

``` bash
$ curl http://localhost:8888/
```

The full stack takes around a minute to be fully functional as there are dependencies beteween services.
After that you should be able to hit Kibana [http://localhost:5601](http://localhost:5601)
You should be able to hit Grafana at [http://localhost:3000](http://localhost:3000). The default username and password is admin/admin.

Before you see the log entries generated before you have to configure an index pattern in kibana. Make sure you configure it with these two options:
* Index name or pattern: logstash-*
* Time-field name: @timestamp


It is remarkable the fact that by default that both kibana and elasticsearch docker images enable by default the xpack plugin, which you will have to pay for it after the trial. This repository disables this paid feature by default. Any addition or change to the kibana behaviour should be perform in this config file.

### Other configuration:
You can configure much more each of the components of the stack. It is up to you and your use case to extend the configuration files and change the *docker-compose.yml* to make it so.
# kelk-grafana
