# TIP
---


# Prog
## 1. Java
---


## 2. Database
### 1. Oracle
### 2. MySQL
---


### 3. FrameWork
#### 1. Spring Boot
##### 1. Spring Project creating
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

##### 2. Spring Security

##### 3. JPA
#### 1. JPA란
  - Object-Relational Mapping (객체 관계 매핑)
    - 객체지향(Java)과 관계형(RDBMS)과의 패러다임 불일치를 해결(매핑)하는 기술로, 본 문서는 Hibernate를 기준으로 작성.
    
  - JPA의 동작과정
    - [ [ Java Application ]  <-JPA->  [ JDBC API ] ]  <->  [ Database ]
    - Application과 JDBC 사이에 위치하여, 객체지향형과 관계형에 대한 불일치 패러다임을 해소.
    
#### 2. Tech
##### 1. Tech
###### 1. 복합키
  - @EmbededId
    ~~~
    ~~~
    
  - @IdClass
    ~~~
    @Entity
    @Table( name = "TB_USER_ROLE" )
    @IdClass( UserRoleCompositeKey.class )
    public class UserRole{
      @Id
      private User user;
      
      @Id
      private Role role;
    }
    
    public class UserRoleCompositeKey implements Serializable{
      public UserRoleCompositeKey(){
      }
      
      public UserRoleCompositeKey( Long userNo, String roleCd ){
        this.user = userNo;
        this.role = roleCd;
      }
      
      private Long user;    // UserRole Class에 정의된 User ReferenceVariable과 동일한 이름을 사용.
                            // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
      private String role;  // UserRole Class에 정의된 Role ReferenceVariable과 동일한 이름을 사용.
                            // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
    }
    ~~~
###### 2. 단방향/양방향 Mapping
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


##### 4. React interlocking
---


## 5. React
### 1. React interlocking in Spring Boot
  - React 앱을 생성
     ~~~
     npm install -i react react-dom
     
     또는
     
     npm install -g create-react-app
     ~~~
     
  - 의존성 추가(Webpack, Babel, Loader)
     ~~~
     npm i webpack webpack-cli @babel/core @babel/preset-env @babel/preset-react babel-loader css-loader style-loader -D
     ~~~
     - babel-loader : 자바스크립트 모듈 번들링을 위한 로더이며, 보통 ES6 코드를 ES5로 변환하기 위해 사용한다.
     - css-loader : 모듈 번들링은 자바스크립트 기반으로 이뤄지기 때문에 CSS 파일을 자바스크립트로 변환하기 위해 사용한다.
     - style-loader : css-loader에 의해 모듈화 되고, 모듈화 된 스타일 코드를 HTML 문서의 STYLE 태그 안에 넣어주기 위해 사용된다.
     - url-loader : 스타일에 설정된 이미지나 글꼴 파일을 문자열 형태의 데이터(Base64)로 변환하여 해당 CSS 파일 안에 포함시켜버리기 때문에 정적 파일을 관리하기 쉬워진다. 하지만 실제 파일들보다 용량이 커지고, CSS 파일이 무거워지므로 적당히 사용하는 것을 권장한다.
     - file-loader : 정적 파일을 로드하는데 사용되며, 보통 프로젝트 환경에 맞게 특정 디렉토리에 파일을 복사하는 역할을 수행한다.
     
  - 의존성 추가(PropTypes, React Router)
     ~~~
     npm install --save prop-types react-router-dom
     ~~~
       - prop-types: React의 Prop를 validation할 때 사용.(React v15.5 부터 다른 패키지로 이동(즉, React.PropTypes사용 불가)하였으며, 현재는 prop-types lib를 사용하는 것을 권고.)
       - react-router-dom: react-router를 의존하고 있기 때문에, 해당 component만 설치한다.
     
### 2. WebPack
  - webpack setting
    * webpack-cli: 터미널에서 webpack 커맨드를 실행할 수 있게 해주는 커맨드 라인 도구.
    - root directory에 'webpack.config.js' file 생성
    - webpack.config.js 작성(파일 참조)
     ~~~
     Source 경로
       - module.exports = {context: path.resolve(__dirname, 'Source 경로') ... }
     Build SRC
       - module.exports = { ... entry: {Build SRC entry: '경로'} ... }
     Build DST
       - module.exports = { ... output: {path: __dirname, filename: '경로'} ... }
     ~~~
      - context: root directory 를 지정한다.
        - entry는 해당 path를 시작으로 진행되나, output은 해당 path를 시작으로 빌드 되지 않는다.(path.resolve(__dirname, 'need path')를 이용) 왜지 ?!
        - ex>
           path.resolve(__dirname, 'src/main/frontend'),
      - resolve: contrext의 대상선정?!
        - ex>
          resolve: {
            extendsion: ['.js', '.jsx']
          }
      - entry: root file로써, entry를 시작으로 필요한 모듈들을 다 불러온 후, 한 파일로 합쳐 bundle.js에 저장.
                추가적으로는 모듈을 통하여 ES6 문법으로 작성된 코드를 ES5 형태로 변환.
        - ex>
          entry: {
            main: './src/App.js'
          }
      - output: 산출물
        - ex>
          const _publicPath = '/public/dist';
          ...
          output: {
            path: path.resolve(__dirname, 'src/main/resources/public/dist'),
            publicPath: _publicPath,
            filename: 'app.bundle.js'
          }
                
  - webpack plug-in
    - clean-webpack-plugin: output으로 지정한 디렉토리를 build할 때마다 삭제하여 주는 plug-in
      ~~~
      npm install --save-dev clean-webpack-plugin
      ~~~
    - html-webpack-plugin: 번들링 시, 기본 HTML문서의 template을 생성해주는 plug-in
      ~~~
      npm install --save-dev html-webpack-plugin
      ~~~
    - html-webpack-root-plugin: html-webpack-plugin을 확장한 plugin으로써, html-webpack-plugin이 생성하는 HTML문서에 elements를 추가할 수 있다.
      ~~~
      npm install --save-dev html-webpack-root-plugin
      ~~~
    - uglifyjs-webpack-plugin: js파일을 난독화 및 압축하여 주는 plug-in
      ~~~
      npm install --save-dev uglifyjs-webpack-plugin
      ~~~
      
### 3. UI
#### 1. Meterial-UI
    - Install
      ~~~
      npm install --save @material-ui/core
      ~~~
      
### 4. Transfer
#### 1. Axios
    - Install
      ~~~
      npm install --save axios
      ~~~


## 별첨1. terms
---


## 99. Server Setting
### 99.1. Database Server
- Architecture  
![Database Server Setting](./../img/Server-Setting.png) 

#### 99.1.1. MySQL 설치
- 참고(MySQL 설치): 

#### 99.1.2. 
- 참고(firewalld 설정): 
    
#### 99.1.3. 
- 참고(VMWare Port Forwarding 설정): 

---


