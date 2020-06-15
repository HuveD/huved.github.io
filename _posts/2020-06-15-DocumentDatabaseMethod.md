---
title:  NoSQL을 알아보자(11) - Document 데이터베이스의 기본 연산
tag: [NoSQL, NoSQL을 알아보자, Document Database]
comments: true
---

Document 데이터베이스도 다른 데이터베이스처럼 4가지 기본 연산을 수행할 수 있다.

## 삽입
<pre>

	db.member.insert({“name” : “John”, “age” : 25})
	
</pre>

위에 예시는 ‘member’ Collection에서 ‘John’이라는 멤버를 추가하는 예시이다. Collection에 Document를 추가할 때는 아래처럼 고유 식별자를 함께 추가하는 것이 좋다.

<pre>

	db.member.insert({“id” : 1321, “name” : “John”, “age” : 25})
	
</pre>

고유 식별자는 사용하는 Document 데이터베이스의 종류에 따라 권장되는 식별자 형태도 다르다. 어떤 유형의 고유 식별자를 사용할지는 직접 사용하는 Document 데이터베이스의 문서를 확인할 필요가 있다.

만약 10개의 데이터 삽입이 필요한 경우 insert 연산을 10번 선언하는 것 보다 insert 명령어 하나에 배열로 값을 전달하는 것이 더 효율적이다. 10번의 insert는 각각 쓰기 명령에 따라 부하가 생기지만, 배열로 한번에 추가하는 경우 최초 한 번만 부하가 발생하기 때문에 더 효율적이다.


## 삭제

<pre>

	db.member.remove({“id” : 1321})
	
</pre>

위에 remove 명령어는 멤버 Collection에서 고유 식별자가 1321인 멤버를 제거하는 예시이다. 만약 remove 명령어의 매개변수가 없이 remove 명령어만 호출된다면 해당 Collection의 모든 Document를 제거하는 명령어가 된다.

remove를 할 때 주의 할 점은 다른 Collection에서 해당 Document를 참조하고 있는지 확인할 필요가 있다. 관계형 데이터베이스는 다른 Table에서 참조되는 항목의 삭제를 방지하도록 설계할 수 있지만, Document 데이터베이스는 참조 여부 상관 없이 데이터가 삭제된다.

그 결과 이미 삭제된 Document를 참조하는 외부 Document가 존재할 수 있으며, 사라진 Document를 요청하는 경우 에러가 발생할 수 있다.

Document 데이터베이스에서는 이 같은 상황에서 무결성을 관리하려면 애플리케이션 단에 의존할 수 밖에 없다.

## 갱신

Document의 갱신은 update 명령어를 사용해 수정할 수 있다. 일반적으로 update는 2개의 매개변수를 요구한다.
- 문서 질의
- 갱신할 Key와 Value

문서 질의는 remove에서 id로 삭제할 Document를 식별한 것처럼, update에서는 갱신할 문서를 식별하기 위해 사용된다. Key와 Value는 갱신한 필드와 갱신할 값을 나타낸다. update로 사용되는 매개변수는 데이터베이스 종류에 따라 추가적인 변수가 있을 수도 있기 때문에 해당 데이터베이스 문서를 확인해 볼 필요가 있다.

## 조회

Document의 조회는 find 명령어를 사용하여 해결 할 수 있다. find 명령어 역시 remove나 update 명령어처럼 지정할 Document를 식별할 문서 질의가 매개변수로 필요하다.

매개변수가 없다면 해당 Collection의 모든 Document를 조회한다는 뜻이고 매개변수로 문서 질의가 있다면 해당하는 Document 만을 조회한다는 뜻이다.

Document를 조회할 때 모든 Key-Value 쌍이 필요하지 않을 때도 있다. 이 경우는 매개변수에 추가로 반환할 Key를 지정하여 해당 항목만 획득하는 것도 가능하다.

데이터 조회에는 조건식과 불 연산자 등을 이용하여 더 복잡한 형태의 데이터를 획득할 수도 있다. 조회에 사용되는 조건식은 각각 사용하는 데이터베이스 문서를 확인하여 참고하면 된다.