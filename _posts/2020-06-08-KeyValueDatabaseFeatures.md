---
title:  NoSQL을 알아보자(6) - Key-Value 데이터베이스 특징
tag: [NoSQL, NoSQL을 알아보자, Key-Value Database]
comments: true
---

Key-Value 데이터베이스의 핵심 특징은 단순성, 속도, 확장성 세 가지다. Key-Value 데이터베이스는 가장 간단한 데이터 구조를 사용한다. 간단한 이미지 자르기를 하는데 전문 그래픽 툴을 쓰는 것보다 그림판으로 작업하는 것이 훨씬 간편한 것처럼 때로는 Key-Value 데이터베이스가 관계형 데이터베이스를 보다 효율적인 경우가 있다.

## 단순함
Key-Value 데이터베이스는 특정 키를 요청하면 해당하는 데이터를 반환하는 아주 단순한 구조다. 배열과 상당히 유사하지만 Key-Value 데이터베이스는 배열보다 훨씬 자유롭다.

예를들어 배열의 경우 크기가 5인 int형 배열이 있다고 가정해보자.

<pre>
    int[] array = new int[5];
</pre>

이렇게 선언된 ‘array’는 5개의 값을 가질 수 있다. 첫 번째 값을 가져오기 위해서는 array[0], 두 번째 값을 가져오기 위해서는 array[1] 이런 방식으로 데이터를 가져올 수 있다.

배열의 값을 불러올 때 사용한 0과, 1 등의 인덱스가 Key-Value 데이터베이스에서는 Key가 된다. 배열에서는 Key(인덱스) 값으로 정수만 사용가능 하지만, Key-Value 데이터베이스는 Key 값으로 숫자와, 문자 등의 다양한 형태를 지원한다.

Key-Value 데이터베이스는에서는 아래와 같은 방식으로 키 값을 사용할 수 있다.

<pre>
    user[“VipUser1”] = “Pino”
</pre>

또 다른 특징은 값에 형태를 제한하지 않는다는 것이다. 위에 크기가 5인 int형 배열 ‘array’의 경우 5개의 공간 모두 int 형태만 저장할 수 있다. 반면 Key-Value 데이터베이스는 같은 공간에 다양한 형태의 데이터를 저장할 수 있다.

<pre>
    user[“VipUser1”] = “Pino”
    user[“VipUser2”] = 910927
    user[929323] = “Jame”
</pre>

위와 같은 예시처럼 Key-Value 데이터는 Key와 Value 형태가 매우 자유롭다.

## 빠른 속도

두 번째 특징으로 Key-Value 데이터베이스는 빠른 속도를 가지고 있다. 연관 배열 데이터 구조와 성능을 최적화하는 설계 덕에 Key-Value 데이터베이스는 빠른 속도를 가지고 있다.

일반적으로 데이터를 읽고 쓰는 것은 디스크보다 램에서 이뤄질때 훨씬 빠르다. 디스크는 전원 연결이 끊어져도 데이터가 유지되지만, 램은 전원 공급이 끊기면 저장된 데이터가 소실된다.  Key-Value 데이터베이스는 메모리(데이터 캐시)와 디스크(DB)를 동시에 사용하고 있어 데이터 처리에 빠른 속도를 보여준다.

하지만 컴퓨터의 메모리 자원은 한정적이기 때문에 데이터를 메모리에 캐시하기 위해서는 저장 공간을 미리 확보해둘 필요가 있다.

## 확장성

세 번째 특징으로는 확장성이 있다. 확장성이 좋다는 것은 시스템의 크기를 확대한 경우만을 얘기하는 것이 아니다. 시스템의 크기를 축소할 때도 유연하게 대응할 수 있어야 확장성이 좋다고 할 수 있다.

Key-Value 데이터베이스는 대표적으로 두 가지 방법을 통해 확장성을 관리 한다.

### 1. Master-Slave 복제

![Master-Slave](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_master_slave.png){: width="65%" height="65%"}{: .center}

일반적인 애플리케이션은 데이터를 쓰는 것보다 읽는 것이 많다. 대부분의 홈페이지들은 데이터의 신규 작성보다는 데이터 읽기가 많이 발생한다. Master-Slave 방식은 쓰기보다는 읽기에 최적화된 구조다.

쓰기와 읽기를 모두 작업할 수 있는 Master는 데이터의 갱신이 발생하면 갱신된 데이터를 다른 서버에 복제하며 Master를 제외한 다른 서버는 오직 읽기 요청만 대응한다. Master-Slave 모델은 단순함으로 확장성을 관리한다.

모든 쓰기 요청은 Master만 처리하기 때문에 쓰기 작업에 대한 서로 다른 서버 간의 충돌을 고려하지 않아도 된다. 확장성은 확보했지만 단일 쓰기 모델에서는 가용성 측면에서 리스크가 발생한다. Master 서버가 만약 고장 나는 경우 해당 클러스터는 쓰기 작업을 수행할 수 없다.

이 같은 문제를 해결하기 위해 Master-Slave 환경에서는 Slave가 메시지를 무작위로 보내면서 상태를 체크하는 방식을 채택하고 있다. 일정 시간 내에 Master 서버로부터 메시지를 받지 못한다면 Master 서버에 문제가 발생했음을 인식한다. 

중단 상태가 인식되면 Slave 서버들은 프로토콜을 초기화하고 Slave 서버들 중 한 개를 Master 서버로 선출하여 클러스터를 복구한다.

이 같은 과정에서 Master 서버의 고장을 인지하는 시간, 새로운 Master를 선출하는 시간 등의 이유로 클러스터가 중단되는 상황이 발생하기 때문에 마스터 서버가 고장 났을 경우는 가용성 측면에서 약점을 보인다.

또 쓰기 작업이 많은 경우 한 서버에서만 작업을 처리하기 때문에 작업 처리 속도가 느려지는 단점도 가지고 있다.


### 2. Masterless 복제

![Master-less](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_masterless.png){: width="65%" height="65%"}{: .center}

Masterless 구조는 이름에서 알 수 있듯 Master가 없이 모든 서버가 읽기 및 쓰기를 수행할 수 있다. Master-Slave 구조에서는 데이터의 갱신이 발생하면 Master 서버는 모든 Slave 서버에 데이터 복제를 한다.

Masterless 서버는 Master 서버가 없기 때문에 데이터 복제를 담당하는 서버는 존재하지 않는다. 특정 서버가 데이터 복제를 담당하는 것이 아닌 구성원 모두가 데이터 복제를 담당한다. 여기서 데이터 복제는 인접한 서버를 위주로 발생하게 된다.

예를 들어 4번 서버의 데이터가 갱신된다면 4번 주변의 3번과 5번 서버로 데이터 복제가 발생한다. 복제본의 수는 관리자가 특정수를 지정할 수 있다. 4개의 복제본을 가지도록 설정하면 4개의 인접한 서버에 데이터 복제가 발생한다.
