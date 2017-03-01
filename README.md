# Apache Kafka

Ansible role to install and configure [Apache Kafka][1] on RHEL / CentOS 7.

[Apache Kafka][1] is a message bus using publish-subscribe topics. Other components and products can consume these messages by subscribing to these topics. Kafka is extremely fast, handling megabytes of reads and writes per second from thousands of clients. Messages are persisted and replicated to prevent data loss. Data streams are partitioned and can be elastically scaled with no downtime.

## Requirements

Platform: RHEL / CentOS 7

Java: Oracle JDK

The Oracle Java 8 JDK role from Ansible Galaxy can be used if one is needed.

`$ ansible-galaxy install sleighzy.java-8`

Apache ZooKeeper

The below zookeeper role from Ansible Galaxy can be used if one is needed.

`$ ansible-galaxy install sleighzy.zookeeper`

## Role Variables
    kafka_version: 0.10.1.0
    kafka_scala_version: 2.11
    kafka_broker_id: 0
    kafka_listener_protocol: PLAINTEXT
    kafka_listener_hostname: localhost
    kafka_listener_port: 9092
    kafka_num_network_threads: 3
    kafka_dir: /usr/share/kafka
    kafka_log_dirs: /var/lib/kafka-logs
    kafka_num_partitions: 1
    kafka_log_retention_hours: 168
    kafka_auto_create_topics_enable: true
    kafka_delete_topic_enable: true
    kafka_default_replication_factor: 1
    kafka_zookeeper_connect: 'localhost:2181'
    kafka_zookeeper_connection_timeout: 6000
    kafka_bootstrap_servers: 'localhost:9092'
    kafka_consumer_group_id: kafka-consumer-group

## Starting and Stopping Kafka Services
* The Kafka service can be started via: `systemctl start kafka`
* The Kafka service can be stopped via: `systemctl stop kafka`

## Default Properties

| Property | Value |
|----------|-------|
| ZooKeeper connection | localhost:2181 |
| Kafka bootstrap servers | localhost:9092 |
| Kafka consumer group Id | kafka-consumer-group |
| Kafka broker id | 0 |
| Number of partitions | 1 |
| Data log file retention period | 168 hours |
| Enable auto topic creation | true |
| Enable topic deletion | true |

#### Ports

| Port | Description |
|------|-------------|
| 9092 | Kafka listener port |


#### Directories and Files

| Directory / File | |
|-----|----|
| Kafka installation directory (symlink to installed version) | `/usr/share/kafka` |
| Kafka configuration file | `/usr/share/kafka/config/server.properties` |
| Directory to store data files | `/var/lib/kafka/logs` |
| Kafka service | `/usr/lib/systemd/system/kafka.service` |


[1]: http://kafka.apache.org/
