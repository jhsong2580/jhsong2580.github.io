---
title:  "[카프카] [실습] 4. ElasticSearch 간단 구축 "
excerpt: "아파치 카프카 애플리케이션 프로그래밍 4장"
post-order: 18

tag : ["kafka"]
sidebar:
  nav: "docs"
categories: ["book", "_kafka_application_programing"]


date: 2023-08-15
last_modified_at: 2023-08-15
---

# 4-실습. elasticSearch 구축

1. ElasticSearch 계정 설정

    ```java
    ## 기본 shell이 /bin/sh로 되어있으니, 편하게 사용하시려면 /bin/bash 로 변경해주세용
    sudo useradd -m elasticsearch 
     
    sudo su - elasticsearch
    ```

2. ElasticSearch Endpoint 확인
- [엘라스틱서치 Release Page](https://www.elastic.co/kr/downloads/past-releases#elasticsearch)
- 위 페이지의 설치하고 싶은 버전에서, 각자 OS에 맞는 링크를 복사
  - [https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.4.3-linux-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz)
1. Endpoint로부터 ElasticSearch 파일 다운로드 및 압축해제

```java
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz
tar -xvf elasticsearch-7.6.2-linux-x86_64.tar.gz
```

1. ElasticSearch 실행
- Systemctl을 통한 서비스 등록
  1. Systemctl 서비스 파일 생성

      ```java
      sudo su -
      vi /etc/systemd/system/elasticsearch.service
      
      --- 아래와 같이 입력
      
      [Unit]
      Description=Elasticsearch
      Wants=network-online.target
      After=network-online.target
      
      [Service]
      Type=simple
      User=elasticsearch
      ExecStart=/home/elasticsearch/elasticsearch-7.6.2/bin/elasticsearch --quiet
      Restart=on-failure
      RestartSec=5
      StartLimitInterval=60s
      StartLimitBurst=3
      
      [Install]
      WantedBy=multi-user.target
      ```

  2. 새로운 서비스 파일 등록에 의한 Daemon Reload 및 재시작시 자동 시작 설정

      ```java
      sudo systemctl daemon-reload
      sudo systemctl enable elasticsearch
      ```

  3. 시작 & 상태확인

      ```java
      sudo systemctl start elasticsearch
      sudo systemctl status elasticsearch
      
      --- 한 2분정도 후
      
      curl localhost:9200
      {
        "name" : "MyName",
        "cluster_name" : "elasticsearch",
        "cluster_uuid" : "jDQYDxoJQi2M3fBzmcMO1Q",
        "version" : {
          "number" : "7.6.2",
          "build_flavor" : "default",
          "build_type" : "tar",
          "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
          "build_date" : "2020-03-26T06:34:37.794943Z",
          "build_snapshot" : false,
          "lucene_version" : "8.4.0",
          "minimum_wire_compatibility_version" : "6.8.0",
          "minimum_index_compatibility_version" : "6.0.0-beta1"
        },
        "tagline" : "You Know, for Search"
      }
      ```

  4. 보안설정
    1. elasticsearch 보안설정 켜기

        ```java
        //elasticsearch.yml
        xpack.security.enabled: true
        ```

    2. elasticsearch 재시작

        ```java
        systemctl restart elasticsearch
        ```

    3. password 설정

        ```java
        bin/elasticsearch-setup-passwords interactive 
        ```