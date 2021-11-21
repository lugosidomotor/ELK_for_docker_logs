##Delete old indices:

```sh
curator_cli --host localhost:9200 --dry-run delete_indices --filter_list '[
    {
        "filtertype": "pattern",
        "kind": "prefix",
        "value": "filebeat-"
    },
    {
        "filtertype": "age",
        "source": "name",
        "direction": "older",
        "timestring": "%Y.%m.%d",
        "unit": "days",
        "unit_count": 90
    }
]'
```
```sh
# open the crontab in your text editor
crontab -e
```
```sh
# delete FileBeat indices older then 90 days from ElasticSearch (every day at 1am)
0 1 * * * curator_cli --host localhost:9200 --dry-run delete_indices --filter_list '[{"filtertype": "pattern", "kind": "prefix", "value": "filebeat-"}, {"filtertype": "age", "source": "name", "direction": "older", "timestring": "%Y.%m.%d", "unit": "days","unit_count": 90}]'
```

##Using a Different Index for some Applications:
```sh
output.elasticsearch:
  hosts: ["elasticsearch:9200"]
  indices:
    - index: "filebeat-elastic-%{[agent.version]}-%{+yyyy.MM.dd}"
      when.or:
        - equals:
            container.image.name: docker.elastic.co/beats/filebeat:7.2.0
        - equals:
            container.image.name: docker.elastic.co/elasticsearch/elasticsearch:7.2.0
        - equals:
            container.image.name: docker.elastic.co/kibana/kibana:7.2.0
    - index: "filebeat-apps-%{[agent.version]}-%{+yyyy.MM.dd}"
```
##Sources:
- https://www.sarulabs.com/post/5/2019-08-12/sending-docker-logs-to-elasticsearch-and-kibana-with-filebeat.html
- https://github.com/marcel-dempers/docker-development-youtube-series/tree/master/monitoring/logging
- https://appfleet.com/blog/docker-centralized-logging-with-elk-stack/
- https://ridwanfajar.medium.com/send-your-container-logs-to-elk-elasticsearch-logstash-and-kibana-with-gelf-driver-7995714fbbad
- https://github.com/albertllousas/centralized-logs-with-EFK-and-docker
- https://github.com/jerfletcher/elk-docker-log
- https://programmaticponderings.com/2017/04/10/streaming-docker-logs-to-the-elastic-stack-using-fluentd/
- https://docs.fluentd.org/v/0.12/articles/docker-logging-efk-compose
- https://levelup.gitconnected.com/centralize-your-docker-logging-with-fluentd-a2b7e0a379ce
- https://blog.logicwind.com/efk-setup/
- https://coralogix.com/blog/managing-docker-logs-with-elk-and-fluentd/
