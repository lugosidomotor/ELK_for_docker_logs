Delete old indices:

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

Using a Different Index for some Applications
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
