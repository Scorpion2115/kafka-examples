# Working with the Confluent Kafka REST APIs

## Enable confluent services
Ensure the `docker-compose.yml` contains `schema-registry` and `kafka-rest` image

## Create a topic
Create a topic to use for testing.
```bash
kafka-topics --bootstrap-server broker:9092 --create --topic guard --partitions 1 --replication-factor 1

kafka-topics --bootstrap-server broker:9092 --create --topic forward --partitions 1 --replication-factor 1

```

## Producing message with REST Proxy
1. Publish some messages to the topic using the Confluent REST proxy. If succeed, you should get a response containing metadata about the two new messages. 
```bash
curl -X POST -H "Content-Type: application/vnd.kafka.json.v2+json" \
  -H "Accept: application/vnd.kafka.v2+json" \
  --data '{"records":[{"key":"4","value":"JP"},{"key":"5","value":"ZX"}]}' "http://0.0.0.0:8082/topics/guard"

curl -X POST -H "Content-Type: application/vnd.kafka.json.v2+json" \
  -H "Accept: application/vnd.kafka.v2+json" \
  --data '{"records":[{"key":"10","value":"WL"},{"key":"16","value":"LL"}]}' "http://0.0.0.0:8082/topics/forward"

```
2. Then you can sse a console consumer to verify that your messages are present in the topic.
```bash
kafka-console-consumer --bootstrap-server broker:9092 --topic guard  --from-beginning --property print.key=true

kafka-console-consumer --bootstrap-server broker:9092 --topic forward  --from-beginning --property print.key=true
```

## Consuming message with REST Proxy
1. Create a consumer and a consumer instance that will start from the beginning of the topic log.
```bash
curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" \
  --data '{"name": "myteam_consumer_instance", "format": "json", "auto.offset.reset": "earliest"}' "http://localhost:8082/consumers/myteam_json_consumer"
``` 
2. Subscribe the consumer to the topic.
```bash
curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" \
  --data '{"topics":["forward"]}' "http://localhost:8082/consumers/myteam_json_consumer/instances/myteam_consumer_instance/subscription"
```

3. Consume the messages.
```bash
curl -X GET -H "Accept: application/vnd.kafka.json.v2+json" "http://localhost:8082/consumers/myteam_json_consumer/instances/myteam_consumer_instance/records"
```  
4. When you are finished using the consumer, close it to clean up.
```bash
curl -X DELETE -H "Content-Type: application/vnd.kafka.v2+json" "http://localhost:8082/consumers/myteam_json_consumer/instances/myteam_consumer_instance"
```