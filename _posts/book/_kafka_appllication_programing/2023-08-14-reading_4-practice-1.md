---
title:  "[카프카] [실습] 4. Kafka Cluster 구축 "
excerpt: "아파치 카프카 애플리케이션 프로그래밍 4장"
post-order: 17

tag : ["kafka"]
sidebar:
  nav: "docs"
categories: ["book", "_kafka_application_programing"]


date: 2023-08-14
last_modified_at: 2023-08-14
---

# 4-실습2-1. Kafka Cluster 구축
- 자바 설치가 되어있어야 합니다. (java 11)
- KAFKA는 2.5.0 Release를 사용하였습니다…

### Kafka 설치

---

- Kafka 설치

    ```java
    wget https://archive.apache.org/dist/kafka/2.5.0/kafka-2.5.0-src.tgz
    tar -xvf kafka-2.5.0-src.tgz
    ```


### Zookeeper 설정

---

### vi {{Kafka Directory}}/config/zookeeper.properties

  | 설정          | 설명                                             |
  |--------------|--------------------------------------------------|
  | dataDir      | Zookeeper 서버가 동작 중에 생성되는 중요한 파일들을 저장하는 경로 |
  | server.#     | #번 Zookeeper 노드 정보                                 |


### dataDir 내 저장되는 파일

  | 파일/디렉토리   | 설명                                                              |
  |----------------|-------------------------------------------------------------------|
  | snapshot.*     | 현재 Zookeeper 서버의 데이터 상태를 저장하는 스냅샷 파일            |
  | log.*          | 업데이트 작업을 기록하는 트랜잭션 로그 파일                         |
  | myid           | 클러스터 내에서 해당 서버를 고유하게 식별하는 서버 ID를 저장하는 파일  |
  | version-2/     | Zookeeper 서버의 내부 데이타와 메타데이터를 저장하는 디렉토리       |

### server.# 에 저장되는 포트 설명

  | 포트 번호      | 설명                                            |
  |--------------|-------------------------------------------------|
  | 2888         | 서버 간 데이터 동기화 및 통신에 사용되는 포트   |
  | 3888         | 리더 선출을 위한 통신에 사용되는 포트           |


```java
dataDir={{Zookeeper DataDir Absolute Path}}
clientPort={{Zookeeper Service Port}}
maxClientCnxns=0
admin.enableServer=false
# 팔로워가 리더와 초기에 연결하는 시간에 대한 타임아웃
initLimit=5
# 팔로워가 리더와 동기화 하는데에 대한 타임아웃. 즉 이 틱 시간안에 팔로워가 리더와 동기화가 되지 않는다면 제거 된다.
syncLimit=2

server.1='Zookeeper#1 IP':'Zookeeper Sync Port':'Zookeeper Leader Election Port'
server.2='Zookeeper#2 IP':'Zookeeper Sync Port':'Zookeeper Leader Election Port'
server.3='Zookeeper#3 IP':'Zookeeper Sync Port':'Zookeeper Leader Election Port'
```

### Zookeeper Cluster 내 ID 설정

---

```java
cd 'Zookeeper DataDir Absolute Path'

---
# Zookeeper Node 별 다른값 입력 필수

### Zookeeper#1
touch myid && echo "1" > myid

### Zookeeper#2
touch myid && echo "2" > myid

### Zookeeper#3
touch myid && echo "3" > myid
```

### Zookeeper 시작 (각 클러스터 별)

---

```java
'Kafka Path'/bin/zookeeper-server-start.sh 'Kafka Path'/config/zookeeper.properties
```

### Broker 설정

---

vi 'Kafka Directory'/config/server.properties

| 속성 | 역할 및 용도 |
| --- | --- |
| listeners | 브로커가 클라이언트 요청을 수신할 호스트 및 포트를 지정합니다. (0.0.0.0:9092) |
| advertised.listeners | 클라이언트가 브로커에 접속할 때 사용할 호스트 및 포트 정보를 지정 (application.yml에 적는 주소와 동일) |
| broker.id | 각 브로커 서버 별 다른 ID를 가져야 한다.  |
| zookeeper.connect | 클러스터와 연결할 주키퍼들 |
| log.dirs | Kafka가 메시지를 저장하는 로그 파일의 디렉토리 경로 |

log.dirs에 저장되는 파일들

| 파일 종류 | 역할 및 용도 |
| --- | --- |
| Segment 파일 | 메시지를 연속적으로 저장하며, 크기 또는 시간에 따라 분할되는 파일. 메시지의 스냅샷을 저장하고 관리. |
| Offset 파일 | Segment 파일 내의 메시지 오프셋과 실제 파일 내 위치를 매핑하는 인덱스 파일. 검색을 빠르게 함. |
| Time Index 파일 | 시간 기반 검색을 지원하기 위해 메시지의 타임스탬프와 오프셋 정보를 저장하는 인덱스 파일. |
| Snapshot 파일 | Segment 파일의 인덱스 정보를 저장하여 빠른 복구 및 검색을 지원하는 파일. 주기적으로 생성됨. |

```java
broker.id=0
log.dirs=/home/ubuntu/test/kafka-logs
listeners=PLAINTEXT://:9096
advertised.listeners=PLAINTEXT://localhost:9096
zookeeper.connect='Zookeeper#1 IP':'Zookeeper Port', 'Zookeeper#2 IP':'Zookeeper Port', 'Zookeeper#3 IP':'Zookeeper Port'
```

### Broker 시작 (각 클러스터 별)

---

```java
'Kafka Path'/bin/kafka-server-start.sh 'Kafka Path'/config/server.properties
```