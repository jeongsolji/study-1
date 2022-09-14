

# JPA 소개
## 패러다임의 불일치
### 상속
- JPA는 상속과 관련된 패러다임의 불일치 문제를 개발자 대신 해결해준다.
  - 객체 : 상속
  - 데이터베이스 : 슈퍼타입과 서브타입 관계를 이용하여 상속을 유사한 형태로 설계
```
# 객체
abstract clas Item{
  Long id;
  String name;
  int price;
}

class Album extends Item{
  String artist;
}

class Movie extends Item{
  String director;
  String actor;
}

class Book extends Item{
  String author;
  String isbn;
}

# 데이터베이스

```

# JPA 시작
## 객체 매핑 시작 

## persistence.xml 설정
- JPA의 설정정보를 관리하는 설정파일.
- Persistence class를 사용하여 사용할 수 있게한다.

## 어플리케이션 개발
### 엔티티 매니저 설정(with 엔티티 매니저 팩토리)
- persistence.xml -Persistence.createEntityManagerFactory()-> EntityManagerFactory -this.createEntityManager()-> EntityManager
- EntityManagerFactory
  - Thread Safety
- EntityManager
  - No Thread Safety

### 트랜잭션 관리
- EntityManager -this.getTransaction()-> EntityTransaction
```
EntityTransaction tx = em.getTransaction();
try{
  tx.begin();
  // TODO
  tx.commit();
}catch(Exception e){
  tx.rollback();
}
```
- QnA: Service의 트랜잭션과 EntityManager의 트랜잭션의 관계는 무언가가 있을까 ? 이게 궁금하다.

### 비즈니스 로직

### JPQL
- SQL을 추상화한 객체지향 쿼리 언어로, JPQL에서는 데이터베이스의 정보를 전혀 알지 못한다(Table, Column 등)
- JPQL은 SQL과 문법의 거의 유사하지만 분명한 차이가 존재한다.
  - SQL: 데이터베이스의 테이블을 대상으로 쿼리한다.
  - JPQL: 엔티티객체를 대상으로 쿼리한다. 쉽게 이야기해서 클래스와 필드를 대상으로 쿼리한다.
  ```
  # SQL
  select *  from member    -- 여기서 member는 데이터베이스의 member테이블을 의미한다.
  
  # JPQL
  select *  from member    -- 여기서 member는 application의 member entity class를 의미한다.
  ```
  
# 영속성 관리
## 엔티티 매너지 팩토리와 엔티티 매니저
- persistence.xml -Persistence.createEntityManagerFactory()-> EntityManagerFactory -this.createEntityManager()-> EntityManager -used conn-> DB접근
                  
- EntityManagerFactory
  - Thread Safety
- EntityManager
  - No Thread Safety
  
## 영속성 컨텍스트란?

## 엔티티의 생명주기

## 영속성 컨텍스트의 특징

## 플러시

## 준영속
