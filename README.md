# IBM MQ Troubleshooting

This repository tackles several questions regarding Kafka Connect and
the IBM MQ Connector.

* [IBM MQ Source Connector](https://docs.confluent.io/kafka-connectors/ibmmq-source/current/overview.html)
* [IBM MQ Sink Connector](https://docs.confluent.io/kafka-connectors/ibmmq-sink/current/overview.html)

You can start the environment with:

```shell
docker-compose up -d
```


## JVM Metrics via JMX Exporter

### Useful Resources
* [Kafka Monitoring and Metrics Using JMX with Docker documentation](https://docs.confluent.io/platform/current/installation/docker/operations/monitoring.html)
* [JMX Exporter Repository](https://github.com/prometheus/jmx_exporter)
* [jmx-monitoring-stacks Repository](https://github.com/confluentinc/jmx-monitoring-stacks/tree/7.2-post/shared-assets/jmx-exporter)

### JConsole
We want to run the JMX Exporter as the Java agent, therefore
we need to download the Jar.
From the [jmx-monitoring-stacks repository](https://github.com/confluentinc/jmx-monitoring-stacks/tree/7.2-post/shared-assets/jmx-exporter)
we copy the corresponding yaml files.

We add to the `docker-compose` file

```yaml
volumes:
    - ./Metrics/jmx-exporter/:/usr/share/jmx-exporter
...
EXTRA_ARGS:
    -javaagent:/usr/share/jmx-exporter/jmx_prometheus_javaagent-0.18.0.jar=1234:/usr/share/jmx-exporter/broker.yml
```

We can now start JConsole with the command `jconsole localhost:9991` and see memory metrics.

### Prometheus

We configure the `./Prometheus/prometheus.yml` and 
add to the `docker-compose` file

```yaml
prometheus:
  image: prom/prometheus:main
  hostname: prometheus
  container_name: prometheus
  ports:
    - 9090:9090
  volumes:
    - ./Metrics/Prometheus:/etc/prometheus
```

We can now start Prometheus under `localhost:9090` and scrape the memory information
per CP component via:

```
jvm_memory_bytes_used
```

### Grafana

We configure the `./grafana` and
add to the `docker-compose` file

```yaml
grafana:
  image: grafana/grafana:8.1.3
  container_name: grafana
  environment:
    - "GF_SECURITY_ADMIN_USER=admin"
    - "GF_SECURITY_ADMIN_PASSWORD=password"
    - "GF_USERS_ALLOW_SIGN_UP=false"
  ports:
    - 3000:3000
  volumes:
    - ./grafana/provisioning/:/etc/grafana/provisioning
```

We can now start Grafana under `localhost:3000` with admin/password and 
see all Connect metrics in the corresponding dashboard.

![](connect_memory.png)


## IBM MQ Source extract payload

