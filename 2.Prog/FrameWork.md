# FrameWork
  - Spring MVC
  - Spring Boot(Core, Security, JPA)
  
---


# Spring MVC

# Spring Boot

## core
  - 참고사이트: [https://blog.woniper.net/336](https://blog.woniper.net/336)
  
  
## Spring Project creating
  - Spring Initializr 사용방법(2가지)
    ~~~
    1. Spring 공식 홈페이지(spring.io)에서 제공하는 기능을 사용합니다.
      1-1. spring.io 접속
      1-2. projects(상단메뉴) > spring boot(조회된 메뉴) > Quick start(상세 탭) > Spring Initializr 이용
       
      2. IDE(Intelli-J, STS 등)에서 제공하는 기능을 사용합니다.
        2-1. 각 tools마다 사용법 상이.
    ~~~
      
  - Spring Initializr Description
    ~~~
    - Project: Maven Project 또는 Gradle Project
      - 빌드, 베포 툴인 Maven과 Gradle 중 택
    - Language: Java, Kotlin, Groovy
    - Spring Boot: Spring Boot의 version
    - Project Metadata: group, artifact 등을 지정합니다.
      - 일반적으로 group은 project의 도메인 및 Default package 경로를 뜻하며, artifact는 프로젝트 명을 암시합니다.
    - Dependencies
      - 프로젝트의 의존성을 추가합니다. (간단히 소프트웨어의 플러그인 정도로 생각하면 됩니다.)
    
  - Spring Boot Config
    - 개념
      - .properties
      - yaml
        - 파일명 또는 경로변경/추가
        ```console
        [user@localhost ~]# java -jar myproject.jar --spring.config.name=myproject.yaml
        --------------------------------------
          - Describe
            - 파일명 변경
        
        
        [user@localhost ~]# java -jar myproject.jar --spring.config.location=classpath:/myproject/{또는 파일명 변경 시, classpath:/myproject/myproject.yaml}
        --------------------------------------
          - Describe
            - 파일경로 변경
          - Result: 기존 4개 경로에서, Customizing된 경로 1개로 변경된다.
            - 기존 경로(4개)
              file:./config/
              file:./
              classpath:/config/
              classpath:/
            - Customizing되어 변경된 경로(1개)
              classpath:/myproject/
        
        
        [user@localhost ~]# java -jar myproject.jar --spring.config.additional-location=classpath:/custom-config/,file:./custom-config/
        --------------------------------------
          - Describe
            - 파일경로 추가
          - Result
            - file:./custom-config/
              classpath:custom-config/
              file:./config/
              file:./
              classpath:/config/
              classpath:/
        ```
      - 환경변수
      - command line args
      
    - 사용방법
    ```console
    [user@localhost ~]# java -jar -Dspring.profiles.active=dev board-0.0.1-SNAPSHOT.jar
    
    또는
    
    [user@localhost ~]# java -jar -Dspring-boot.run.profiles=dev board-0.0.1-SNAPSHOT.jar
    ```
      
  - 참고사이트: [https://www.latera.kr/reference/java/2019-09-29-spring-boot-config-externalize/](https://www.latera.kr/reference/java/2019-09-29-spring-boot-config-externalize/)
  
## profiles with maven3
  - You can provide commandline argument like this:
  ```console
  mvn spring-boot:run -Dspring-boot.run.arguments="--spring.profiles.active=dev"
  ```
  - You can provide JVM argument like this:
  ```console
  mvn spring-boot:run -Dspring-boot.run.jvmArguments="-Dspring.profiles.active=dev"
  ```
  - java -jar
  ```console
  java -Dspring.profiles.active=dev -jar app.jar (Note order)
  
  or
  
  java -jar app.jar --spring.profiles.active=dev (Note order)
  ```

## Bean
### Bean Hocker
#### BeanDefinitionRegistryPostProcessor
  - Bean 등록 목적
#### BeanFactoryPostProcessor
  - Bean 정의를 재정의 또는 속성추가 목적
#### BeanPostProcessor
  - Bean 인스턴스를 재정의 목적
  
  - 참고사이트: [https://thecodinglog.github.io/spring/2019/01/29/dynamic-spring-bean-registration.html](https://thecodinglog.github.io/spring/2019/01/29/dynamic-spring-bean-registration.html)

### Conditional Bean Registration(조건부 빈 등록)
#### Java
  - 참고사이트: [https://sodocumentation.net/ko/spring/topic/4732/spring%EC%9D%98-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EB%B9%88-%EB%93%B1%EB%A1%9D](https://sodocumentation.net/ko/spring/topic/4732/spring%EC%9D%98-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EB%B9%88-%EB%93%B1%EB%A1%9D)
  ```console
  public class PropertyCondition implements Condition {
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
          return context.getEnvironment().getProperty("propertyName") != null;
          // optionally check the property value
      }
  }

  @Configuration
  public class MyAppConfig {
      @Bean
      @Conditional(PropertyCondition.class)
      public MyBean myBean() {
        return new MyBean();
      }
  }

  @Configuration
  public class MyAppConfig {
      @Bean
      @Conditional({PropertyCondition.class, SomeOtherCondition.class})
      public MyBean myBean() {
        return new MyBean();
      }
  }
  ```

#### Annotation
##### Class Conditional
  - @ConditionalOnClass
  - @ConditionalOnMissingClass
  ```console
  @Bean
  @ConditionalOnWebApplication
  @ConditionalOnClass(OObjectDatabaseTx.class)
  @ConditionalOnMissingBean(OrientWebConfigurer.class)
  public OrientWebConfigurer orientWebConfigurer() {
      return new OrientWebConfigurer();
  }
  ```
  
##### Bean Conditional
  - @ConditionalOnBean
  - @ConditionalOnMissingBean
  
##### Property Conditional
  - @ConditionalOnProperty
  ```console
  @ConditionalOnProperty(value='somebean.enabled', matchIfMissing = true, havingValue="yes")
  @Bean 
  public SomeBean someBean(){
  }
  ```
  
##### Resource Conditional
  - @ConditionalOnResource
  ```console
  @ConditionalOnResource(resources = "classpath:init-db.sql") 
  ```
  
##### WebApplication Conditional
  - @@ConditionalOnWebApplication
  - @ConditionalOnNotWebApplication
  ```console
  @Configuration
  @ConditionalOnWebApplication
  public class MyWebMvcAutoConfiguration {...}
  ```
  
##### Expression Conditional
  - @ConditionalOnExpression
  ```console
  @ConditionalOnExpression("${rest.security.enabled}==false")
  ```

## Transactional
### Prior knowledge
  - ACID(원자성, 일관성, 고립성, 지속성): transaction이 안전하게 수행된다는 것을 보장하기 위한 성질
    - Atomicity(원자성): 트랜잭션은 하나의 원자처럼 취급되어야 한다.
	                       즉 부분적으로 실행되어, 특정 부분만 반영되고 나머지는 반영되지 않으면 안된다는 뜻.
	                       예> 돈을 송금할 때, 송금하는 쪽은 성공하고 받는 쪽이 실패했다고 끝내면 안된다.
    - Consistency(일관성) : 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 Scheme 상태로 유지하는 것을 의미한다.
	                       예> 돈 100원을 송금할 때, 돈은 int형이여야 하지만 프로그램상 실수로 문자열 자료형으로 보냈을 경우, DB에 문자열 100이 저장되기 때문에 data의 일관성이 깨지기 때문에 트랜잭션이 취소되어야 한다.
    - Isolation(독립성)   : 트랜잭션을 수행 시 다른 트랜잭션의 연산 작업이 끼어들지 못하도록 독립되어 있다는 것을 보장한다.
	                         이것은 트랜잭션 밖에 있는 어떤 연산도 중간 단계의 데이터를 볼 수 없음을 의미한다.
	                         예> 법인카드의 잔여금액인 100원을 Read하여 총 3번 연산을 한다. 각 연산은 100원씩 증가를 시킨다.
	                             100 -> 200 -> 300 -> 400
	                             만약 300원째 계산을 하고 있을 때, 다른 사람이 잔여금액을 확인한다고 해도, 트랜잭션은 독립적으로 움직이기 때문에 100원으로 보인다.

    - Durability(지속성)  : 성공적으로 수행된 트랜잭션은 영원히 반영되어야 함을 의미하며, 시스템 문제, DB 일관성 체크 등을 하더라도 유지되어야 함을 의미한다.
	  			                  전형적으로 모든 트랜잭션은 로그로 남고 시스템 장애 발생 전 상태로 되돌릴 수 있다.
		  		                  트랜잭션은 로그에 모든 것이 저장된 후에만 commit 상태로 간주될 수 있다.
  
### Transactional Annotation
#### Level
  - 0Lv, DEFAULT
  - 1Lv, READ_UNCOMMITED
  - 2Lv, READ_COMMITED
  - 3Lv, REPEATABLE_READ
  - 4Lv, SERIALIZABLE

  - 참고사이트: [https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation](https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation)

#### propagation
  - Spring의 @Transactional의 propagation 속성으로 다음과 같은 설정
  - Propagation
    - REQUIRED : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성
    - REQUIRES_NEW : 부모 트랜잭션을 무시하고 무조건 새로운 트랜잭션이 생성
    - SUPPORT : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 nontransactionally로 실행
    - MANDATORY : 부모 트랜잭션 내에서 실행되며 부모 트랜잭션이 없을 경우 예외가 발생
    - NOT_SUPPORT : nontransactionally로 실행하며 부모 트랜잭션 내에서 실행될 경우 일시 정지
    - NEVER : nontransactionally로 실행되며 부모 트랜잭션이 존재한다면 예외가 발생
    - NESTED : 해당 메서드가 부모 트랜잭션에서 진행될 경우 별개로 커밋되거나 롤백될 수 있음. 둘러싼 트랜잭션이 없을 경우 REQUIRED와 동일하게 작동

    - 참고사이트: [https://supawer0728.github.io/2018/03/22/spring-multi-transaction/](https://supawer0728.github.io/2018/03/22/spring-multi-transaction/)

## Spring Security

## JPA
  - Object-Relational Mapping (객체 관계 매핑)
    - 객체지향(Java)과 관계형(RDBMS)과의 패러다임 불일치를 해결(매핑)하는 기술로, 본 문서는 Hibernate를 기준으로 작성.
    
  - JPA의 동작과정
    - [ [ Java Application ]  <-JPA->  [ JDBC API ] ]  <->  [ Database ]
    - Application과 JDBC 사이에 위치하여, 객체지향형과 관계형에 대한 불일치 패러다임을 해소.
    
### Tech
#### 복합키
  - @EmbededId
    ~~~
    ~~~
    
  - @IdClass
    - Identifying Relationship
      - 참고사이트: [https://woowabros.github.io/experience/2019/01/04/composit-key-jpa.html](https://woowabros.github.io/experience/2019/01/04/composit-key-jpa.html)
      
    - None Identifying Relationship
    ~~~
    @Entity
    @Table(name = "tb_mem_user")
    public class User extends BaseEntity {
        @Id
        @GeneratedValue(
                strategy = GenerationType.AUTO
        )
        @Column(name = "user_id", nullable = false)
        private Long userId;
    
    
        public Long getUserId() {
            return userId;
        }
    
        public User setUserId(Long userId) {
            this.userId = userId;
            return this;
        }
    }
    
    
    @Entity
    @Table(name = "tb_rom_room")
    public class Room extends BaseEntity {
        @Id
        @Column(name = "room_id", nullable = false)
        private String roomId;
    
    
        public String getRoomId() {
            return roomId;
        }
    
        public Room setRoomId(String roomId) {
            this.roomId = roomId;
            return this;
        }
    }
    
    
    public class UserRoomComposite implements Serializable {
        public UserRoomComposite(){
        }
    
        public UserRoomComposite(Long userId, String roomId){
            this.user = userId;
            this.room = roomId;
        }
    
    
          private Long user;    // UserRole Class에 정의된 User ReferenceVariable과 동일한 이름을 사용.
                                // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
          private String room;  // UserRole Class에 정의된 Role ReferenceVariable과 동일한 이름을 사용.
                                // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
    
    
        public Long getUser() {
            return user;
        }
    
        public UserRoomComposite setUser(Long user) {
            this.user = user;
            return this;
        }
    
        public String getRoom() {
            return room;
        }
    
        public UserRoomComposite setRoom(String room) {
            this.room = room;
            return this;
        }
    
    
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            UserRoomComposite userRoomComposite = (UserRoomComposite) o;
            return Objects.equals(this.user, userRoomComposite.getUser()) &&
                    Objects.equals(this.room, userRoomComposite.getRoom());
        }
    
        @Override
        public int hashCode() {
            return Objects.hash(this.user, this.room);
        }
    
    
        @Override
        public String toString() {
            return "UserRoomComposite{" +
                    "user=" + user +
                    ", room='" + room + '\'' +
                    '}';
        }
    }
    
    
    @Entity
    @Table(name = "tm_mem_user_room")
    @IdClass(value = UserRoomComposite.class)
    public class UserRoom extends BaseEntity {
        @Id
        @ManyToOne
        @JoinColumn(name = "user_id")
        private User user;
    
        @Id
        @ManyToOne
        @JoinColumn(name = "room_id")
        private Room room;
    
    //    @OneToMany(mappedBy = "userRoom")
    //    private List<SprinklingToken> sprinklingTokenList = new ArrayList<>();
    
    
        public User getUser() {
            return user;
        }
    
        public UserRoom setUser(User user) {
            this.user = user;
            return this;
        }
    
        public Room getRoom() {
            return room;
        }
    
        public UserRoom setRoom(Room room) {
            this.room = room;
            return this;
        }
    }
    ~~~
#### 단방향/양방향 Mapping
  - 단방향 Mapping
    ~~~
    ~~~
    
  - 양방향 Mapping
    ~~~
    @Entity
    @Table( name = "TB_COM_USER" )
    public class User{
      @Id
      @Column( name = "USER_NO" )
      private Long userNo;
    }
    
    @Entity
    @Table( name = "TB_COM_ROLE" )
    public class Role{
      @Id
      @Column( name = "ROLE_CD" )
      private String roleCd;
    }
    
    @Entity
    @Table( name = "TB_USER_ROLE" )
    @IdClass( UserRoleCompositeKey.class )
    public class UserRole{
      @Id
      @ManyToOne
      @JoinColumn( name = "USER_NO", referencedColumnName = "USER_NO" )  // UserRole Table에 'USER_NO" 컬럼생성(name 속성)
                                                                         // User Table(referenceVariable의 Class Type 참조)의 'USER_NO' 컬럼참조(referencedColumnName 속성)
      private User user;
      
      @Id
      @ManyToOne
      @JoinColumn( name = "ROLE_CD", referencedColumnName = "ROLE_CD" )  // UserRole Table에 'ROLE_CD" 컬럼생성(name 속성)
                                                                         // Role Table(referenceVariable의 Class Type 참조)의 'ROLE_CD' 컬럼참조(referencedColumnName 속성)
      private Role role;
    }
    
    public class UserRoleCompositeKey implements Serializable{
      public UserRoleCompositeKey(){
      }
      
      public UserRoleCompositeKey( Long userNo, String roleCd ){
        this.user = userNo;
        this.role = roleCd;
      }
      
      private Long user;
      private String role;
    }
    
    - JoinColumn
      - name : 외래키로 정의 할, 자신의 테이블의 컬럼명(Column Name) 설정
      - referencedColumnName : 외래키로 참조할, 대상 테이블의 컬럼명 설정
      - foreignKey(DDL): 외래 키 제약조건명 설정(테이블을 생성조건에만 사용가능)
      - unique : @Column의 속성과 동일
      - nullable : @Column의 속성과 동일
      - insertable : @Column의 속성과 동일
      - updatable : @Column의 속성과 동일
      - columnDefinition : @Column의 속성과 동일
      - table : @Column의 속성과 동일
    ~~~
