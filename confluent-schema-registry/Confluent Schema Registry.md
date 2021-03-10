# Confluent Schema Registry

## Creating an Avro Schema
The location of the file is important, because it will be used by the `build.gradle`
The correct location of avro file is `src/main/avro` + `package path`
```bash
# make a correct directory to store avro schema
mkdir -p src/main/avro/ace/tpg/developer

# create a new schema
vi src/main/avro/ace/tpg/developer/Person.avsc
```

## Using Schema Registry with a Kafka Producer
Add the Confluent repository, Avro plugin, and Avro dependencies. Then run the following command to obtain the Gradle wrapper:
```bash
gradle wrapper
```

Create a directory for the Java Class files in this project. Edit the ProducerMain class.
```shell
mkdir -p src/main/java/ace/tpg/developer
```
![img](./img/project-structure.png?raw=true "project structure")

Create the employees topic to use for testing.
```bash
kafka-topics --bootstrap-server broker:9092 --create --topic employees --partitions 1 --replication-factor 1
```

Run your code
```bash
./gradlew runProducer
```

Verify that the messages published by the producer are present in the topic.
```bash
kafka-console-consumer --bootstrap-server broker:9092 --topic employees --from-beginning
```