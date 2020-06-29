---
title:  NoSQL을 알아보자(13) - Column Family 데이터베이스 등장 배경
tag: [NoSQL, NoSQL을 알아보자, Document Database]
comments: true
---
## 등장 배경

Column Family 데이터베이스는 대용량 데이터베이스(VLDB, Very Large Data Base)의 필요성이 부각되며 등장했다. 수백, 수천만 명을 대상으로 한 애플리케이션은 기존 관계형 데이터베이스로 관리하는데 상당한 어려움이 있었다.

한 테이블에 수 억개의 Row들과 수만 개의 Column들이 있는 대규모의 데이터 셋을 유연하게 관리하면서, 확장성이 높고, 관계형 데이터베이스처럼 질의까지 가능한 데이터베이스가 필요했다.

Key-Value 데이터베이스는 훌륭한 확장성과 속도, 유연성을 제공하여 대규모 데이터를 처리하는데는 이점이 있지만, 자주 함께 쓰이는 Column들을 구조화하여 사용하는데 어려움이 있다.

Document 데이터베이스는 유연성을 제공하면서 연관 Column들을 묶어서 한 문서에 표현할 수 있어 Key-Value 데이터베이스의 단점을 보완하지만, 대규모 Column이 포함된 문서를 질의하는데 어려움이 있다.

Graph 데이터베이스는 데이터의 이동 방향과 주변 관계를 표현하는데는 적합했지만 대규모의 구조적인 데이터를 저장하고 조작하는데 어려움이 있다. Graph 데이터베이스 설계 목적부터 근접한 객체를 모델링하기 위해 탄생한 데이터베이스다.

근접한 객체를 모델링할 때는 더욱 간편하고 효율적으로 질의할 수 있어 쓰이지만, 이외 관계를 표현할 때는 오히려 복잡하고 비효율적인 질의를 사용해야 되기 때문에 대규모 데이터를 표현하는데는 적합하지 않았다.

기존의 관계형 데이터베이스를 단점을 보완하여 분산 데이터 처리를 목표로 등장한 다른 NoSQL 기반 데이터베이스들도 VLDB에 적합하지 않았고, 그 결과 등장한 것이 Column Family 데이터베이스다.

* Column Family 데이터베이스는 2006년 ‘BigTable: A Distributed Storage System for Structured Data(빅테이블: 구조화된 데이터를 위한 분산 스토리지 시스템)’에서 처음 등장한 새로운 유형의 데이터베이스다.

* Key-Value 데이터베이스, Document 데이터베이스, Graph 데이터베이스의 용어들은 표준으로 정해졌지만 Column Family 데이터베이스의 용어는 아직 표준으로 정해지지 않았다. Column Family Database, Wide Column Family Database, Column Family Datastore 등등으로 표현되고 있다.

* 대표적으로 Bigtable, Cassandra, HBase, Accumulo 등이 Column Family 데이터베이스에 속한다.