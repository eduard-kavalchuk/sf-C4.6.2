Configure Rsyslog to send Nginx logs to Elasticsearch and visualize them in Kibana.

Make initial setup of Rsyslog (for details see [this](https://luvpreetsingh.github.io/nginx-to-rsyslog/) link) to enable it to normalize log messages and send logs to Elasticsearch:
```
sudo add-apt-repository ppa:adiscon/v8-stable
sudo apt-get update
sudo apt-get install rsyslog-mmnormalize rsyslog-elasticsearch
```
Replace ```/etc/rsyslog.conf``` file with provided ```rsyslog/rsyslog.conf```. It tells rsyslog to listen to nginx logs via UDP on port 514 or unix socket.

Put ```rsyslog/70-output.conf``` and ```rsyslog/01-json-template.conf``` files to ```/etc/rsyslog.d``` directory to tell Rsyslog for every incoming message interpolate log properties into a JSON formatted message, and forward it to Logstash, listening on port 10514 (see [this](https://devconnected.com/monitoring-linux-logs-with-kibana-and-rsyslog/#b_Routing_from_rsyslog_to_Logstash) link for more details).

Tell Nginx to push its logs to Rsyslog by adding the following line to all sites-enabled config files that need to be tracked (see ```nginx/default``` file as an example):
```
access_log syslog:server=127.0.0.1:514,facility=local7,tag=nginx,severity=info;
```

Install and configure Elasticsearch, Logstash and Kibana as described [here](https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elastic-stack-on-ubuntu-20-04).

Put provided ```logstash/logstash.conf``` file to ```/etc/logstash/conf.d/logstash.conf``` directory.

In Kibana manually add ```logstash-*``` index pattern.
