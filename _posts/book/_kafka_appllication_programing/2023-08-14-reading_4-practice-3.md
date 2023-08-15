---
title:  "[카프카] [실습] 4. Kibana 간단 구축 "
excerpt: "아파치 카프카 애플리케이션 프로그래밍 4장"
post-order: 19

tag : ["kafka"]
sidebar:
  nav: "docs"
categories: ["book", "_kafka_application_programing"]


date: 2023-08-15
last_modified_at: 2023-08-15
---

# 4-실습1-5. Kibana 설정 및 ElasticSearch 연동

1. kibana 계정 설정

```java
## 기본 shell이 /bin/sh로 되어있으니, 편하게 사용하시려면 /bin/bash 로 변경해주세용
sudo useradd -m kibana 
 
sudo su - kibana
```

1. Kibana설치 (elasticSearch 버전과 맞아야 한다.)

   →  [데브원영님 블로그](https://blog.voidmainvoid.net/329)

- 현재 ElasticSearch 버전 : v7.6.2
- 설치할 Kibana 버전 : v7.6.0

```java
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.6.0-amd64.deb
dpkg -i kibana-7.6.0-amd64.deb (루트권한 필요)

sudo systemctl daemon-reload
systemctl enable kibana
service kibana start
systemctl status kibana
```

1. Kibana 설정 변경
  - elasticsearch host는 각자 설정하시면 됩니다

```java
vi /etc/kibana/kibana.yml

server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

1. Kibana Service 파일 변경
- 위와 같이 설정하면 kibana service file에는 config파일 위치가 적혀있지 않습니다.

```java
1. Kibana Config파일 위치 확인
ls -al /etc/kibana/kibana.yml 
-rw-rw---- 1 root kibana 4979 Aug 12 10:07 /etc/kibana/kibana.yml

2. Kibana Service 파일 위치 확인
systemctl status kibana
● kibana.service - Kibana
     Loaded: loaded (/etc/systemd/system/kibana.service; enabled; vendor preset: enabled)

3. Service 파일 열고 ExecStart 변경 후 저장
vi /etc/systemd/system/kibana.service

ExecStart=/usr/share/kibana/bin/kibana --config /etc/kibana/kibana.yml

4. Daemon 파일 재적용 및 kibana restart
sudo systemctl daemon-reload
systemctl restart kibana
```

1. 서비스 확인

```java
lsof -i :5601
COMMAND    PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
node    105884 kibana   18u  IPv4 843775      0t0  TCP *:5601 (LISTEN)

```