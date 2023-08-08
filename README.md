# IBM MQ Troubleshooting

This repository tackles several questions regarding Kafka Connect and
the IBM MQ Connector.

* [IBM MQ Source Connector](https://docs.confluent.io/kafka-connectors/ibmmq-source/current/overview.html)
* [IBM MQ Sink Connector](https://docs.confluent.io/kafka-connectors/ibmmq-sink/current/overview.html)

You can start the environment with:

```shell
docker-compose up -d
```

and deploy the IBM MQ Source connector with:

```shell
docker exec -it connect curl -d "@/ibmmq/ibmmq-source.json" -X PUT -H "Content-Type: application/json" http://connect:8083/connectors/ibmmq-source/config
```

## Content
* [JVM memory analysis](Memory/README.md)
  * JVM monitoring
  * Memory heap dump analysis
* [Java Message Service (JMS) handling](JMS/README.md)
  * Payload extraction with SMT
