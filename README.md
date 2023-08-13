# Apache Kafka

![Lint Code Base] ![Molecule]

Ansible role to install and configure [Apache Kafka] 3.5.1

[Apache Kafka] is a distributed event streaming platform using publish-subscribe
topics. Applications and streaming components can produce and consume messages
by subscribing to these topics. Kafka is extremely fast, handling megabytes of
reads and writes per second from thousands of clients. Messages are persisted
and replicated to prevent data loss. Data streams are partitioned and can be
elastically scaled with no downtime.

## WARNING

This Ansible role does not handle the migration process of upgrading from older
versions of Kafka. Please ensure that you read the upgrade documentation and
update the relevant configuration files before running this role.

<https://kafka.apache.org/35/documentation.html#upgrade>

For example, depending on how you upgrade, the `server.properties` file may need
the following properties added to reflect your current version prior to running
this Ansible playbook:

- `inter.broker.protocol.version`
- `log.message.format.version`

## Supported Platforms

- RedHat 6
- RedHat 7
- RedHat 8
- Debian 10.x
- Ubuntu 18.04.x
- Ubuntu 20.04.x

## Requirements

- [Apache ZooKeeper]
- Java 8 (deprecated) / 11 / 17

The below Apache ZooKeeper role from Ansible Galaxy can be used if one is
needed.

```sh
ansible-galaxy install sleighzy.zookeeper
```

Ansible 2.9.16 or 2.10.4 are the minimum required versions to workaround an
issue with certain kernels that have broken the `systemd` status check. The
error message "`Service is in unknown state`" will be output when attempting to
start the service via the Ansible role and the task will fail. The service will
start as expected if the `systemctl start` command is run on the physical host.
See <https://github.com/ansible/ansible/issues/71528> for more information.

## Role Variables

| Variable                                       | Default                              | Comments                                                         |
| ---------------------------------------------- | ------------------------------------ | ---------------------------------------------------------------- |
| kafka_download_base_url                        | <https://downloads.apache.org/kafka> |                                                                  |
| kafka_download_validate_certs                  | yes                                  |                                                                  |
| kafka_version                                  | 3.5.1                                |                                                                  |
| kafka_scala_version                            | 2.13                                 |                                                                  |
| kafka_create_user_group                        | true                                 |                                                                  |
| kafka_user                                     | kafka                                |                                                                  |
| kafka_group                                    | kafka                                |                                                                  |
| kafka_root_dir                                 | /opt                                 |                                                                  |
| kafka_dir                                      | {{ kafka_root_dir }}/kafka           |                                                                  |
| kafka_start                                    | yes                                  |                                                                  |
| kafka_restart                                  | yes                                  |                                                                  |
| kafka_log_dir                                  | /var/log/kafka                       |                                                                  |
| kafka_broker_id                                | 0                                    |                                                                  |
| kafka_java_heap                                | -Xms1G -Xmx1G                        |                                                                  |
| kafka_background_threads                       | 10                                   |                                                                  |
| kafka_listeners                                | PLAINTEXT://:9092                    |                                                                  |
| kafka_num_network_threads                      | 3                                    |                                                                  |
| kafka_num_io_threads                           | 8                                    |                                                                  |
| kafka_num_replica_fetchers                     | 1                                    |                                                                  |
| kafka_socket_send_buffer_bytes                 | 102400                               |                                                                  |
| kafka_socket_receive_buffer_bytes              | 102400                               |                                                                  |
| kafka_socket_request_max_bytes                 | 104857600                            |                                                                  |
| kafka_replica_socket_receive_buffer_bytes      | 65536                                |                                                                  |
| kafka_data_log_dirs                            | /var/lib/kafka/logs                  |                                                                  |
| kafka_num_partitions                           | 1                                    |                                                                  |
| kafka_num_recovery_threads_per_data_dir        | 1                                    |                                                                  |
| kafka_log_cleaner_threads                      | 1                                    |                                                                  |
| kafka_offsets_topic_replication_factor         | 1                                    |                                                                  |
| kafka_transaction_state_log_replication_factor | 1                                    |                                                                  |
| kafka_transaction_state_log_min_isr            | 1                                    |                                                                  |
| kafka_log_retention_hours                      | 168                                  |                                                                  |
| kafka_log_segment_bytes                        | 1073741824                           |                                                                  |
| kafka_log_retention_check_interval_ms          | 300000                               |                                                                  |
| kafka_auto_create_topics_enable                | false                                |                                                                  |
| kafka_delete_topic_enable                      | true                                 |                                                                  |
| kafka_default_replication_factor               | 1                                    |                                                                  |
| kafka_group_initial_rebalance_delay_ms         | 0                                    |                                                                  |
| kafka_zookeeper_connect                        | localhost:2181                       |                                                                  |
| kafka_zookeeper_connection_timeout             | 6000                                 |                                                                  |
| kafka_bootstrap_servers                        | localhost:9092                       |                                                                  |
| kafka_consumer_group_id                        | kafka-consumer-group                 |                                                                  |
| kafka_server_config_params                     |                                      | General dictionary that will be templated into server.properties |

See [log4j.yml](./defaults/main/002-log4j.yml) for detailed  
log4j-related available variables.

## Starting and Stopping Kafka services using systemd

- The Kafka service can be started via: `systemctl start kafka`
- The Kafka service can be stopped via: `systemctl stop kafka`

## Starting and Stopping Kafka services using initd

- The Kafka service can be started via: `service kafka start`
- The Kafka service can be stopped via: `service kafka stop`

## Default Properties

| Property                       | Value                |
| ------------------------------ | -------------------- |
| ZooKeeper connection           | localhost:2181       |
| Kafka bootstrap servers        | localhost:9092       |
| Kafka consumer group ID        | kafka-consumer-group |
| Kafka broker ID                | 0                    |
| Number of partitions           | 1                    |
| Data log file retention period | 168 hours            |
| Enable auto topic creation     | false                |
| Enable topic deletion          | true                 |

### Ports

| Port | Description         |
| ---- | ------------------- |
| 9092 | Kafka listener port |

### Directories and Files

| Directory / File                                             |                                         |
| ------------------------------------------------------------ | --------------------------------------- |
| Kafka installation directory (symlink to installed version)  | `/opt/kafka`                            |
| Kafka configuration directory (symlink to /opt/kafka/config) | `/etc/kafka`                            |
| Directory to store data files                                | `/var/lib/kafka/logs`                   |
| Directory to store logs files                                | `/var/log/kafka`                        |
| Kafka service                                                | `/usr/lib/systemd/system/kafka.service` |

## Example Playbook

Add the below to a playbook to run those role against hosts belonging to the
`kafka-nodes` group.

```yaml
- hosts: kafka-nodes
  roles:
    - sleighzy.kafka
```

## Linting

Linting should be done using [ansible-lint].

```sh
pip3 install ansible-lint --user
```

## Testing

This module uses the [Ansible Molecule] testing framework. This test suite
creates a Kafka and ZooKeeper cluster consisting of three nodes running within
Docker containers. Each container runs a different OS to test the supported
platforms for this Ansible role.

As per the [Molecule Installation guide] this should be done using a virtual
environment. The commands below will create a Python virtual environment and
install Molecule including the Docker driver.

```sh
$ python3 -m venv molecule-venv
$ source molecule-venv/bin/activate
(molecule-venv) $ python3 -m pip install molecule-docker "molecule[docker,lint]"
```

Run playbook and tests. Linting errors need to be corrected before Molecule will
execute any tests. This will run all tests and then destroy the Docker
containers.

```sh
molecule test
```

The below command can be used to run the playbook without the tests. This can be
run multiple times when making changes to the role, and ensuring that operations
are idempotent.

```sh
molecule converge
```

The below commands can be used to just run the tests without tearing everything
down. The command `molecule verify` can be repeated for each test run.

```sh
molecule create
molecule converge
molecule verify
```

Tear down Molecule tests and Docker containers.

```sh
molecule destroy
```

## License

[![MIT license]](https://lbesson.mit-license.org/)

[ansible-lint]: https://docs.ansible.com/ansible-lint/
[ansible molecule]: https://molecule.readthedocs.io/
[apache kafka]: http://kafka.apache.org/
[apache zookeeper]: https://zookeeper.apache.org/
[lint code base]:
  https://github.com/sleighzy/ansible-kafka/workflows/Lint%20Code%20Base/badge.svg
[molecule]:
  https://github.com/sleighzy/ansible-kafka/workflows/Molecule/badge.svg
[molecule installation guide]:
  https://molecule.readthedocs.io/en/stable/installation.html
[mit license]: https://img.shields.io/badge/License-MIT-blue.svg
