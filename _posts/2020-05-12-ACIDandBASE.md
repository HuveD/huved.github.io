---
title: NoSQL을 알아보자(4) - ACID와 BASE
tag: [NoSQL, NoSQL을 알아보자]
comments: true
---

일반적으로 관계형 데이터베이스는 ACID 트랜잭션을 지원하도록 설계됐으며, NoSQL 기반 데이터베이스는 BASE 트랜잭션을 지원하도록 설계됐다. 일부 NoSQL 데이터베이스는 완벽히는 아니지만 ACID 트랜잭션을 지원하지도 한다.

## ACID 트랜잭션
### A(atomicity)
더는 분할되지 않는 단위인 원자성을 의미한다. 여기서 원자성은 하나의 트랜잭션을 더이상 쪼갤 수 없는 최소 단위로 여기는 것이다. 하나의 트랜잭션은 내부에 여러 과정이 있을 수 있지만 전체적으로는 하나의 동작으로 처리되어야 한다.

![Atomicity Example1](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_atomicity1.png){: width="65%" height="65%"}{: .center}

내 계좌에 있는 100만원을 저금하는 것을 하나의 트랜잭션이라 가정해보자. 해당 트랜잭션은 내부적으로 2개의 단계로 나눠질 수 있다. 먼저 내 계좌에서 100만원을 출금을 한뒤 은행 잔고에 100만원을 입금하는 방식이다.

<br/>

![Atomicity Example2](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_atomicity2.png){: width="750%" height="75%"}{: .center}

이 예시에서 원자성은 ‘100만원 저금’이라는 하나의 행위를 최소의 단위로 여기는 것이다. ‘100만원을 출금’, ‘100만원을 입금’ 두 가지 내부 과정은 최소 단위가 되어선 안된다. 만약 100만원 출금만 성공하고 100만원 입금은 실패한 경우, 100만원만 빠져나가고 은행 잔고는 그대로인 경우가 발생하면 안된다. 

출금, 입금이 모두 성공인 경우 ‘100만원 저금’이라는 전체적인 관점에서는 저금 성공이다. 출금은 성공하고 입금은 실패인 경우 ‘100만원 저금’이라는 전체적인 관점에서 저금 실패다. 저금이라는 행위에서 부분 성공, 부분 실패는 있을 수 없다.

즉, 원자성은 트랜잭션(하나의 행위)이 부분 수행되는 것을 방지하는 능력, 최소한의 단위라고 볼 수 있다.

### C(consistency)
데이터의 일관성을 의미한다. 트랜잭션 과정동안 데이터 무결성이 위반되지 않는다는 의미를 가지고 있다. 위에 저금 예시에서 저금이 실행되기 전 상태 또는 저금이 완료된 상태 두 가지 상태를 보장하는 것을 일관성이라고 한다.

즉, 내 계좌에서 100만원이 출금은 됐지만 은행 잔고로 100만원이 입금은 아직 안된 중간 상태를 표시하지 않도록 일관성을 보장한다.

### I(isolation)
고립성을 의미하며 고립된 트랜잭션은 작업이 종료될 때까지 다른 사용자에게 보이지 않는다. 내 계좌에서 100만원이 출금은 됐지만 은행 잔고로 100만원이 입금은 아직 안된 중간 상태에서는 다른 사람들이 계좌의 잔고를 조회할 수 없다.

### D(durability)
지속성을 의미한다. 하나의 트랜잭션이나 작업이 완료되면 서버가 중단되더라도 데이터가 완료된 상태로 남아있는 것을 의미한다.

## BASE 트랜잭션
### BA(basic available)
분산 데이터베이스 시스템에서 부분적인 오작동이 발생하여 실패는 있을 수 있지만, 나머지 서비스는 계속 기능을 수행한다는 의미다.

### S(soft state)
컴퓨터 공학에서 소프트 상태는 새로 고쳐지지 않으면 데이터가 소실된다는 뜻이다. NoSQL에서 소프트 상태는 이전 데이터가 더 최신 데이터로 덮어쓰인다는 것을 의미한다. 아래 설명할 결과적 일관성과 관련이 깊다.

### E(eventually consistent)


![Eventually Consistent Example1](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_eventually_consistent.png){: width="40%" height="40%"}{: .center}

결과적 일관성은 데이터베이스가 일관성 없는 상태를 가지는 순간이 있다는 것을 의미한다. NoSQL 데이터베이스는 서버 여러 대에 데이터 복사본을 두고 관리하는 경우가 있다. 예시 (1) 에서 데이터베이스1에 100만원 송금에 대한 정보를 저장하였다. 

관계형 데이터베이스의 경우는 1~3 세 단계를 하나의 작업으로 여기기 때문에(ACID의 원자성 특징) 작업이 완료된 직후 데이터베이스2 또는 데이터베이스3의 정보를 조회하면 원본 데이터베이스와 일치하는 것을 확인할 수 있다.

반면 NoSQL 데이터베이스에선 (1)의 송금 작업이 끝난 직후 데이터베이스2 또는 데이터베이스3의 정보를 조회하면 송금 이력이 반영되지 않았을 수도 있다. 데이터 복제 작업이 완료되지 않은 시간 동안은 데이터 비일관성이 발생한다.

이전 데이터가 최신 데이터로 덮어 쓰이면서 복제 작업이 모두 완료되면 결과적으로 데이터는 일관성을 가진다. 이 같은 특징을 결과적 일관성이라고 한다.
