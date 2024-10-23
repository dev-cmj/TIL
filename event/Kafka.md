# Apache Kafka

아파치 카프카는 실시간 데이터 스트리밍을 위한 분산 메시징 플랫폼이다.
주로 대량의 데이터를 빠르고 안정적으로 처리하는데 사용, 이벤트 스트리밍 아키텍처에서 중요한 역할을 한다.

* 이벤트 스트리밍 아키텍처란?

애플리케이션 간의 데이터 통신을 비동기적으로 처리하고 발생하는 이벤트를 실시간으로 스트리밍하는 방식으로 데이터를 처리하는 시스템 구조이다.
시스템 내의 각종 이벤트가 발생하면 해당 이벤트를 실시간으로 스트리밍하여 다른 서비스나 애플리케이션에서 즉각적으로 처리할 수 있게 한다.

## 핵심 개념

1. Producer (생산자)

- Producer는 Kafka에 메시지를 보내는 역할을 한다. 데이터나 이벤트를 kafka로 전송하는 애플리케이션을 의미한다.
- 웹 애플리케이션 서버는 사용자 활동을 Kafka로 보내는 Producer 역할을 한다.

2. Consumer (소비자)

- Consumer는 Kafka에서 메시지를 소비하는 역할을 한다. Kafka에서 받은 데이터를 처리하거나 다른 시스템으로 전달하는 역할을 한다.
- Consumer는 메시지를 읽고 이를 기반으로 다양한 비즈니스 로직을 처리할 수 있다. 예를 들어 실시간 로그 분석 시스템에서 Kafka로부터 로그 데이터를 읽어 처리하는 Consumer을 만들 수 있다.

3. Broker (브로커)

- Kafka Broker는 Kafka의 핵심 역할을 담당하는 서버이다. Producer가 보낸 메시지를 저장하고, Consumer에게 메시지를 전달하는 중개 역할을 한다.
- 하나의 Kafka 클러스터는 여러 개의 Broker로 구성되어있다.

```plaintext
Kafka Cluster
├── Broker 1
├── Broker 2
└── Broker 3
```

4. Topic (토픽)

- Topic은 Kafka에서 메시지를 구분하는 단위이다. Topic은 일종의 카테고리로 각 Producer는 특정 Topic에 메시지를 전송하고, Consumer는 해당 Topic에서 메시지를 읽는다.
- Topic은 여러개의 Partition으로 나눠진다.
- Topic은 클러스터 내에서 특정 데이터를 구분하는 논리적 단위이다.

```plaintext
Topic: order_topic
├── Partition 0
├── Partition 1
└── Partition 2
```

5. Partition (파티션)

- Partition은 Topic을 여러 개의 물리적인 파티션으로 나눈 것이다. Partition은 메시지를 저장하는 단위이다.
- Partition을 사용하면 Topic 내에서 메시지를 병렬로 처리할 수 있어서 수평적으로 확장이 가능하다.
- Partition을 통해 데이터를 분산 저장하고, 여러 Consumer 가 동시에 메세지를 읽도록 하여 처리 속도를 높일 수 있다.

```plaintext
Broker 1
├── Partition 0 (Leader)
└── Partition 1 (Replica)

Broker 2
├── Partition 1 (Leader)
└── Partition 0 (Replica)

Broker 3
├── Partition 2 (Leader)
└── Partition 0 (Replica)
```

데이터 흐름의 이해

Producer가 order_topic에 데이터를 전송하면, 해당 데이터는 여러 파티션으로 분산된다. Kafka는 각 메시지를 특정 파티션에 할당하고, 이를 브로커에 저장한다.
Consumer는 이 파티션에서 데이터를 읽고, 각 Consumer는 특정 파티션을 읽도록 할당될 수 있다.

6. Offset (오프셋)

- Offset은 Partition 내에서 메시지의 위치를 나타내는 식별자이다. Consumer는 Offset을 통해 메시지를 읽는다.
- Kafka는 메시지를 순차적으로 읽을 수 있도록 Offset을 관리하며 각 Consumer는 어디까지 메시지를 읽었는지 기억하고 이어서 메시지를 처리할 수 있다.


7. Replication (복제)

- Replication은 Kafka의 고가용성을 보장하기 위한 기능이다. Replication은 Partition의 복제본을 생성하여 데이터의 안정성을 보장한다.
- Topic의 각 Partition은 리더-팔로워 파티션으로 나뉘며, 리더 파티션이 주로 데이터를 읽고 쓰며, 팔로워 파티션은 리더 파티션의 복제본이다.
- 리더 파티션에 장애가 발생하면 팔로워 파티션 중 하나가 리더로 승격되어 서비스가 중단되지 않도록 한다. (레플리카 세트)

```plaintext
Kafka Cluster
└── Brokers: [Broker 1, Broker 2, Broker 3]

Topic: order_topic
├── Partition 0: Broker 1 (Leader), Broker 2 (Replica)
├── Partition 1: Broker 2 (Leader), Broker 3 (Replica)
└── Partition 2: Broker 3 (Leader), Broker 1 (Replica)
```

- Kafka 클러스터는 여러개의 Broker로 구성되어 있으며, 각 브로커는 여러 파티션을 갖고 있다.
- Topic은 여러 파티션으로 나뉘어 분산 처리 된다.
- 파티션은 브로커에 저장되며, 각 파티션은 리더와 복제본을 통해 데이터 안정성을 유지한다.

## Zookeeper?

Zookeeper는 Kafka 클러스터의 메타데이터와 상태 정보를 관리하는 역할을 한다. Kafka 클러스터의 브로커, 토픽, 파티션 등의 메타데이터를 저장하고, 클러스터 내의 브로커들이 서로 연결되어 있는지
확인한다.

### ZooKeeper Ensemble

- Zookeeper는 보통 3개 이상의 서버로 구성된 Ensemble에서 동작한다.
- 각 서버는 Zookeeper 데이터의 복제본을 가지며, 다수결 투표로 클러스터의 상태를 결정한다.
- 예를들어 5개의 서버로 구성된 Zookeeper Ensemble에서는 3개 이상의 서버가 동작해야 클러스터가 정상적으로 동작한다.1

아파치 카프카는 Zookeeper를 사용하여 브로커의 메타데이터 관리와 파티션 리더 선출을 수행한다.
