---
title:  NoSQL을 알아보자(10) - Document 데이터베이스 특징②
tag: [NoSQL, NoSQL을 알아보자, Document Database]
comments: true
---

## Document
데이터 모델의 구조를 표현하는 Key-Value 형태의 집합이다. 여기서 Key로는 문자열을 사용할 수 있으며, Value에는 기본 데이터 타입(숫자, 문자, 불 등등), 구조(배열, 객체) 등을 저장할 수 있다.

Key-Value 집합으로 이뤄진 Document를 표현할 때는 Json, XML 등을 사용해 데이터 구조를 표현할 수 있다.

### Document 표현 예시

<pre>
    {
        “member_id” : 919232,
        “name” : “Jame”,
        “phone” : “02-321-2312”,
        “visit_history” : [ “2020-05-01”,
                “2020-05-06”,
                “2020-05-13”,
                “2020-05-21” ]
    }
</pre>


## Collection
여러 개의 Document가 모여 하나로 모여 있는 형태를 Collection이라 한다. 일반적으로 유사한 문서들을 하나의 Collection으로 묶어서 데이터베이스를 모델링한다. 즉 Document 데이터베이스에서 모델링은 다양한 문서를 어떻게  Collection으로 묶을지 정의하는 것이다.

Collection은 관계형 데이터베이스에서 Table의 역할을 하고 있다. 관계형 데이터베이스의 Table과 다른 점은 Collection은 안에 포함된 요소들이 꼭 같은 형태를 할 필요는 없다는 것이다. 

관계형 데이터베이스에서 같은 Table 안에 있는 정보들은 모두 똑같은 속성을 가지고 있다. 반면 Collection에서는 Document간 서로 다른 속성을 가질 수도 있다.

### Collection 표현 예시
<pre>
    {
        {
            “member_id” : 919232,
            “name” : “Jame”,
            “phone” : “02-321-2312”,
            “visit_history” : [ “2020-05-01”,
                    “2020-05-06”,
                    “2020-05-13”,
                    “2020-05-21” ]
        }
        {
            “member_id” : 919233,
            “name” : “Yoo”,
            “phone” : “02-441-7464”,
            “locker_id” : “312”,
            “visit_history” : [ “2020-04-01”,
                    “2020-04-06”,
                    “2020-05-11”,
                    “2020-05-13” ]
        }
        {
            “member_id” : 919234,
            “name” : “John”,
            “phone” : “02-323-2122”,
            “locker_id” : “112”,
            “visit_history” : [ “2020-05-11”,
                    “2020-05-16”,
                    “2020-05-23”,
                    “2020-05-27” ]
        }
    }
</pre>


위에 예시로 설명하자면 헬스장에서 ‘Yoo’와 ‘John’ 두 멤버는 개인 락커를 신청하여 데이터베이스에 ‘locker_id’라는 항목이 추가돼 있다. 반명 개인 락커를 신청하지 않은 ‘Jame’는 ‘‘locker_id’라는 항목이 존재하지 않는다.

만약 회원 500명 중 단 100명만 개인 락커를 사용한다고 했을 때 남은 400명의 회원은 ‘locker_id’라는 속성이 필요 없다. 위에 예시처럼 Document 데이터베이스는 같은 Collection 안에 있더라도 서로 다른 속성을 가질 수 있어 데이터 저장의 유연성을 확보할 수 있다는 장점이 있다.

Collection은 서로 다른 속성을 허용하기 때문에 전혀 다른 유형의 Document도 같은 Collection으로 묶을 수 있다. 이 경우 type 속성 필드를 추가하여 각각의 Document가 어떤 정보인지 알 수 있도록 구분하여 처리할 수 있다. 하지만 한 Collection 안에 전혀 다른 유형을 저장하는 것은 권장되지 않는다. 

전혀 다른 유형의 Document A, B가 하나의 Collection에 저장된다고 가정해보자. 서로 다른 Document A, B는 한 데이터 블록에 인접한 상태로 섞여 있을 가능성이 크다. 

![Document-Block](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_doument_data_block.png){: width="65%" height="65%"}{: .center}

만약 데이터 블록에 A와 B과 섞여 저장된 경우를 예를 들어보자. B라는 Document가 필요하여 데이터를 조회하는 경우 디스크는 일정 단위의 데이터 블록을 읽어 데이터를 가져올 것이다. 필요한 데이터는 B인데 디스크 블록을 읽었을 때 전혀 불필요한 데이터 A도 함께 읽게 된다. 이는 즉 성능으로 직결되며 좋지 않은 성능을 끼친다.

단일 Collection에 서로 다른 타입의 Document가 있다고 항상 성능이 안좋은 것은 아니다. 서로 공통적인 속성은 없지만, 데이터를 어떻게 사용하냐에 따라 단일 Collection이 효율적일 때도 있다.

편의점을 예로 들어보자. 만약 편의점에 식품, 담배, 약 이렇게 3가지 종류만 판매한다고 가정했을 때 3가지 제품은 각각 서로 다른 속성을 가진다.

![Document-Collection](https://raw.githubusercontent.com/huved/huved.github.io/master/assets/images/nosql/img_collection.png){: width="65%" height="65%"}{: .center}

위에서 설명한 방식을 따르면 전혀 다른 속성을 지닌 세 종류는 서로 다른 3개의 Collection에 저장하는 것이 성능에 좋다고 판단할 수 있다.

기본적으로 유사한 Document끼리 Collection을 묶는 것이 효율적이지만, 사용 용도에 따라 또 다르다. 속성은 서로 다르지만 주요 기능들이 3가지 요소를 같이 확인해야 되는 경우 세 Collection을 조회하는 것보다 한 Collection을 조회하는 것이 훨씬 빠르다. 

예를 들어 제품의 주된 기능이 아래와 같은 항목인 경우를 생각해보자
- 한 고객이 구매한 평균 가격은?
- 가장 인기 있는 제품은?
- 가장 많이 반품한 제품은?
- 가장 매출이 높은 제품의 유형은?

첫 번째, 두 번째, 세 번째 문제를 답을 해결하기 위해서는 식품, 담배, 약 세 유형의 모든 제품을 확인해야 알 수 있다. 주요 기능의 대다수가 전체 유형을 필요로 하기 때문에 이 경우는 오히려 Collection을 따로따로 나누는 것보다 하나에 제품 Collection에 3가지 유형을 모두 저장하는 것이 효율적이다.

이처럼 필요한 기능에 따라, 데이터 속성에 따라 Collection을 효율적으로 분리를 하는 것이 Document 데이터베이스를 설계의 핵심 요소다.