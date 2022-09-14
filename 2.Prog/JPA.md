

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
```
persistence.xml -Persistence.createEntityManagerFactory()-> EntityManagerFactory -this.createEntityManager()-> EntityManager
```
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
  tx.commit();        // em.flush()가 호출된다.
}catch(Exception e){
  tx.rollback();
}
```
- QnA: Service의 트랜잭션과 EntityManager의 트랜잭션의 관계는 무언가가 있을까 ? 이게 궁금하다.

### 비즈니스 로직
- 별거 없음으로 패애쓰!

### JPQL
- SQL을 추상화한 객체지향 쿼리 언어로, JPQL에서는 데이터베이스의 정보를 전혀 알지 못한다(Table, Column 등)
- JPQL은 SQL로 변환하여 데이터베이스로 질의를 하는 역활을 한다.(JPA의 영속성 컨테스트등과 관계가 없다)
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
```
persistence.xml -Persistence.createEntityManagerFactory()-> EntityManagerFactory -this.createEntityManager()-> EntityManager -use Connection-> DB접근
                                                         └> Connection Pool ------------------------------------> Connection ┘
```
- EntityManagerFactory
  - Thread Safety
- EntityManager
  - No Thread Safety
  
## 영속성 컨텍스트란?
- 직역: 엔티티를 영구 저장하는 환경
- 논리적 개념이다. 뭐.. spring boot의 request context, javascript의 excution context와 같은.. 뭐 좀 다르지만 비슷한 그런 느낌이다.
- 엔티티 매니저를 이용하여 생성, 접근할 수 있다.

## 엔티티의 생명주기
- 비영속: 영속성 컨텍스트와 전혀 관계가 없는 상태
- 영속: 영속성 컨텍스트에 저장된 상태
- 준영속: 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제: 삭제된 상태
- 비영속과 준영속에 차이
  - 별거 없다. 똑같은데 비영속은 그냥 식별자 아이디(@Id가 없거나 있을 수 있고), 준영속은 영속성 컨텍스트에 존재하다가 안한거니 무조건 식별자 아이디(@Id)가 있다고 가정할 수 있는 차이 뿐.
!!!!!그림을 참조하면 이해가 쉽긴한데..패애쓰!!!!!
```
...

Member member = new Member();
member.setName("kim")             // 비영속

em.persist(member)                // 영속
em.detach(member);                // 준영속: 영속성 컨텍스트에 member가 빠진상태
// em.clear();                    // 이렇게 해도 준영속: 영속성 컨텍스트에 관리하는 모든게 없는 상태
// em.close();                    // 이렇게 해도 준영속: 아예 영속성 컨텍스트가 없는 상태

// em.merge(member);              // 준영속을 다시 영속으로 만든다.

em.remove();                      // 삭제
```

## 영속성 컨텍스트의 특징
- 영속성 컨텍스트는 엔티티를 식별자 값(@Id)으로 구분함으로, 반드시 식별자 값이 있어야 한다.
- 아직 JPA영역에 머문 상태로, 실제 DB를 call한건 아니다. 데이터베이스에 강제로 적용할 수 있는데 이걸 플러시(flush)라고 한다.
  - flush는 commit()할 때 호출이 된다.
  - 영속성 컨텍스트는 식별자 값(@Id), 엔티티(@Entity)와 더불어 스탭샷을 관리하는데, flush할 때 스냅샷과 데이터를 비교하여 다를 시 update문을 날려준다. 이걸 dirty checking이라 한다.
- 아래의 매커니즘을 가능토록 한다.
  - 1차 캐시
  - 동일성 보장
  - 트랙잭션을 지원하는 쓰기 지연
  ```
  # member1, member2, member3이 같이 저장되며, rollback시 모두 롤백된다.
  begin();
  
  save(member1);
  save(member2);
  save(member3);
  
  commit();                         // em.flush()가 호출된다.
  ```
  - 변경 감지(=dirty checking)
    - 변경감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용된다.
    - @DynamicInsert, @DynamicUpdate
      - Member class에 3개의 field(name, age, email)이 있다고 가정하자. 이때 email만 변경해도 쿼리는 update member  set name=?, age=?, email? where id=? 로 질의된다.
      - 변경된 column에 대해서만 insert, update가 나가도록 동적쿼리를 만들게 한다. 컬럼이 30개 이상일 때는 이게 더 빠르다고 한다.
  - 지연 로딩

## 플러시
- 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다.
- 순서
  - em.flush() 직접호출 또는 commit() 시 자동호출 또는 JPQL 쿼리 실행 시 flush된 후 JPQL이 실행된다.
    ```
    # JPQL 실행전 flush 되는 이유
    em.persist(member1)     // 영속성 상태이지만 데이터베이스에 저장하지 않은 상태
    
    JPQL 사용!                // member1은 영속성 상태이지만 데이터베이스에는 저장되지 않았기에 없다. 하지만 JPQL은 바로 SQL로 변환하여 데이터베이스로 조회를 질의한다 했다. 이때 데이터가 없음으로 안된다.
    ```
  - 변경 감지가 동작해서 영속성 컨텍스트에 있는 모든 엔티티를 스냅샷과 비교해서 수정된 엔티티를 찾는다. 수정된 엔티티는 수정 쿼리를 만들어 쓰기 지연 SQL저장소에 등록한다.
  - 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송한다.

## 준영속
- 비영속과 준영속에 차이
  - 별거 없다. 똑같은데 비영속은 그냥 식별자 아이디(@Id가 없거나 있을 수 있고), 준영속은 영속성 컨텍스트에 존재하다가 안한거니 무조건 식별자 아이디(@Id)가 있다고 가정할 수 있는 차이 뿐.

# 엔티티 매핑
// 일단 코드로 좀 보자.. 적을게 없다.. 코드로코드로!

# 프록시와 연관관계 관리
## 프록시
- 즉시로딩(Eager): Entity를 조회할 때 연관된 Entity도 함께 조회한다.
- 지연로딩(Lazy): 연관된 Entity를 실제 사용할 때 조회한다.

## 프록시
### 프록시의 기초
- JPA가 데이터베이스를 접근하지 않고, 실제 엔티티 객체도 생성하지 않도록 한다. 대신에 데이테베이스 접근을 위임한 프록시 객체를 반환하게 한다.
```
Member member = em.getReference(Member.class, "member1");         // Member Entity를 접근할 수 있는 프록시 객체를 반환한다.

###
```

### 프록시와 식별자

## 즉시로딩과 지연로딩
- 즉시로딩(Eager): Entity를 조회할 때 연관된 Entity도 함께 조회한다.
- 지연로딩(Lazy): 연관된 Entity를 실제 사용할 때 조회한다.

### 즉시로딩
- XXXToOne 연관관계에서의 Default
- JOIN을 이용하여 성능을 최적화한다.

### 지연로딩
- XXXToMany 연관관계에서의 Default

## 연속성 전이: CASCADE
- 특정 Entity를 영속 상태로 만들 때 연관된 Entity도 함께 영속 상태로 만들고 싶으면, 영속성 전이 기능을 사용한다.
- JPA는 CASCADE옵션으로 영속성 전이를 제공한다.
- 쉽게 말해서 영속성 전이를 사용하면 부모 Entity를 저장할 때 자식 Entity도 함께 저장할 수 있다.
```
# CASCADE Option
ALL       // 모두 적용
PERSIST   // 영속할 때 전이
MERGE     // 병합할 때 전이
REMOVE    // 삭제할 때 전이: 부모 Entity가 삭제되면 자식 Entity도 모두 삭제된다.
REFRESH   // refresh할 때 전이
DETACH    // 준영속할 때 전이
```

## 고아 객체: opphanRemoval
- 부모 Entity와 연관관계가 끊어진 자식 Entity를 자동으로 삭제하는 기능을 제공하는데 이것을 고아객체 제거라 한다.
- 부모 Entity의 컬렉션에서 자식 Entity의 참조만 제거하면 자식 Entity가 자동으로 삭제된다.
```
# Entity
@Entity
public class Parent{
  @Id
  @GeneratedValue
  private Long id;
  
  @OneToMany(mappedBy = "parent", orphanRemoval=true)
  private List<Child> children = new ArrayList<Child>();
  
  ...
}

# Client
Parent parent1 = em.find(Parent.class, id);
parent1.getChildren().remove(0);    // 자식 Entity의 삭제를 진행할 수 있다.

# SQL
delete  from child  where id=?      // 자식 Entity의 삭제가 진행된다.
```

# 객체지향 쿼리 언어
## 객체지향 쿼리 소개
- JPQL
- Criteria
- QueryDSL
- Native SQL
- JDBC 직접사용.(MyBatis같은 SQL 매퍼 프레임워크)
  - JDBC나 MyBatis를 JPA와 함께 사용하면 영속성 컨텍스트를 적절한 시점에 강제로 flush해야 한다. 이런 이슈를 해결하는 방법은 JPA를 위회해서 SQL을 실행하기 직전에 영속성 컨텍스트를 수동으로 flush한 뒤 데이터베이스와 영속성 컨텍스트를 동기화하고 사용하면 된다.

## JPQL(Java Persistence Query Language)
- JVM이 Java Language를 OS에 의존하지 않게 한것과 같은 맥락.
- Entity객체를 조회하는 객체지향 쿼리다. 데이터베이스에 의존적이지 않다.
- 난!!!!! QueryDSL만 사용해서.. 이번에는 패애쓰!!!!!

## Criteria
- JPQL을 편하게 작성하도록 도와주는 API, Builder Class 모음
- 난!!!!! QueryDSL만 사용해서.. 이번에는 패애쓰!!!!!

## QueryDSL
- JPQL을 편하게 작성하도록 도와주는 Builder Class모음. 비표준 오픈소스 프레임워크

## Native SQL
- JPA에서 JPQL대신 직접 SQL을 사용할 수 있다.
- 아래의 경우만 사용한다.
  - 특정 데이터베이스만 지원하는 함수, 문법, SQL Query Hint(단, QueryDSL이 주 개발 프레임워크 일 때, 함수는 뺀다.)
  - 인라인 뷰(from 절의 Sub Query), Union, Intersect를 사용하는 경우
  - Store Procedure를 사용하는 경우

## 객체지향 쿼리 심화
### 벌크연산(Bulk)
- em.executeInsert(), em.executeUpdate() 메소드를 사용한다.(단, executeInsert는 비표준이다.)

---


# Annotation
@PersistenceUnit: Entity Manager Factory를 주입받는다.

@PersistenceContext: Entity Manager를 주입받는다.



