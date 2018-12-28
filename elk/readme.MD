# lauch ELK and DSE
This repository show how to monitore DSE with ELK. JMX with insight monitoring (https://docs.datastax.com/en/dse/6.7/dse-dev/datastax_enterprise/tools/metricsCollector/mcIntroduction.html) DSE 6.7 -> collectd -> logstash and Cassandra log -> filebeat.
I don't use docker logs features, docker is used only to provide an easy way to create a DSE and ELK cluster.

`docker-compose up -d`

## display indices from ES
`curl -X GET 'http://localhost:9200/_cat/indices'`

## bash into node1
`docker exec -it --user=root elk_node1 bash`

## logs
`docker-compose logs`

## kibana URL
http://localhost:5601/app/kibana

# enable filebeat
```bash
docker exec -it --user=root elk_node1 bash
wget -O - https://gist.githubusercontent.com/florent-brosse/3817c068386a333300c0cef3dcd086b6/raw/babb9170b497b08fd161d0b76e41c1d5735a7c92/dse_filebeat.sh | bash
```

```bash
docker exec -it --user=root elk_node2 bash
wget -O - https://gist.githubusercontent.com/florent-brosse/3817c068386a333300c0cef3dcd086b6/raw/babb9170b497b08fd161d0b76e41c1d5735a7c92/dse_filebeat.sh | bash
```

script launched with the previous command
```bash
#!/bin/bash
apt update
apt install curl -y
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.5.4-amd64.deb
dpkg -i filebeat-6.5.4-amd64.deb

cat > /etc/filebeat/filebeat.yml <<EOF
output.elasticsearch:
    enabled: true
    hosts: ["elasticsearch:9200"]
setup.kibana:
  host: "kibana:5601"
setup.dashboards.enabled: true
filebeat.inputs:
    - type: log
      paths:
        - "/var/log/cassandra/system.log*"
      document_type: cassandra_system_logs
      exclude_files: ['\.zip$']
      multiline.pattern: '^TRACE|DEBUG|WARN|INFO|ERROR'
      multiline.negate: true
      multiline.match: after
#    - type: log
#      paths:
#        - "/var/log/cassandra/gc.log.*.current"
#      document_type: cassandra_gc_logs
#      exclude_files: ['\.zip$']
#      multiline.pattern: '^TRACE|DEBUG|WARN|INFO|ERROR'
#      multiline.negate: true
#      multiline.match: after
processors:
    - dissect:
            tokenizer: "%{level} [%{thread}] %{date} %{time} %{service} %{F}:%{L} - %{msg}"
EOF
```

`service filebeat start`



# enable collectd 
```bash
docker exec -it --user=root elk_node1 bash
vim /opt/dse/resources/dse/collectd/etc/collectd/10-statsd.conf
LoadPlugin network
<Plugin network>
    <Server "logstash" "25826">
    </Server>
</Plugin>

dsetool insights_config --mode DISABLED
dsetool insights_config --mode ENABLED_WITH_LOCAL_STORAGE
```


idem for node2


https://datastax.jira.com/browse/DSP-17319

https://datastax.jira.com/browse/DSP-17869







