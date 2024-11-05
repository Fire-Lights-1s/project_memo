# JPQL이란
JPQL은 **엔티티 객체를 대상으로 하는 쿼리 언어**이다.
```sql
select u from User as u where u.age > 18
```

> **JPQL 문법 특징**

- 엔티티 클래스 이름, 엔티티 필드의 대소문자가 일치해야한다. ( 즉, 유저의 나이를 조회하는데 나이 필드가 age면 Age가 아닌 age로 작성 )
- JPQL 키워드는 대소문자 구분하지 않는다. (SELECT, select, From, from)
- 엔티티 객체를 대상으로 하는 쿼리이므로 엔티티 이름을 사용한다. ( 테이블 이름 X )
- 별칭은 필수이다. (여기서는 u),  (as는 생략가능하다.)
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다.
- JPQL은 결국 SQL로 변환된다.

>출처: [https://hstory0208.tistory.com/entry/JPA-JPQL이란-사용방법-기본-문법-총-정리](https://hstory0208.tistory.com/entry/JPA-JPQL%EC%9D%B4%EB%9E%80-%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-%EC%B4%9D-%EC%A0%95%EB%A6%AC) [< Hyun / Log >:티스토리]

