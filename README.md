# explore-confluent

explore confluent

Create a kafka topic

	docker exec -it exploreconfluent_kafka_1 \
	kafka-topics --create --topic foo --partitions 1 --replication-factor 1 \
	--if-not-exists --zookeeper zookeeper:2181

List Kafka topic

	docker exec -it exploreconfluent_kafka_1 \
	kafka-topics --describe --topic foo --zookeeper zookeeper:2181

Produce some messages

	docker exec -it exploreconfluent_kafka_1 \
	bash -c "seq 42 | kafka-console-producer --request-required-acks 1 \
	--broker-list kafka:9092 --topic foo && echo 'Produced 42 messages.'"


Produce some avro message

	docker exec -it exploreconfluent_schema-registry_1 \
	/usr/bin/kafka-avro-console-producer \
	  --broker-list kafka:9092 --topic bar \
	  --property schema.registry.url=http://schema-registry:8081 \
	  --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}]}'

Type in some message after the previous command

{"f1": "value1"}
{"f1": "value2"}
{"f1": "value3"}

Create a consumer instance.

	docker exec -it exploreconfluent_kafka-rest_1 \
	curl -X POST -H "Content-Type: application/vnd.kafka.v1+json" \
	  --data '{"name": "my_consumer_instance", "format": "avro", "auto.offset.reset": "smallest"}' \
	  http://kafka-rest:8082/consumers/my_avro_consumer

Retrieve data

	docker exec -it exploreconfluent_kafka-rest_1 \
	curl -X GET -H "Accept: application/vnd.kafka.avro.v1+json" \
	  http://kafka-rest:8082/consumers/my_avro_consumer/instances/my_consumer_instance/topics/bar
