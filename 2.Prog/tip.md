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


##### 4. React interlocking
---


## 5. React
### 1. React App
  - React 앱을 생성
     ~~~
     npm install -i react react-dom
     
     또는
     
     npm install -g create-react-app
     ~~~
     
  - 의존성 추가(Webpack, Babel, loader 등)
     ~~~
     npm i webpack webpack-cli @babel/core @babel/preset-env @babel/preset-react babel-loader css-loader style-loader -D
     ~~~
     - babel-loader : 자바스크립트 모듈 번들링을 위한 로더이며, 보통 ES6 코드를 ES5로 변환하기 위해 사용한다.
     - css-loader : 모듈 번들링은 자바스크립트 기반으로 이뤄지기 때문에 CSS 파일을 자바스크립트로 변환하기 위해 사용한다.
     - style-loader : css-loader에 의해 모듈화 되고, 모듈화 된 스타일 코드를 HTML 문서의 STYLE 태그 안에 넣어주기 위해 사용된다.
     - url-loader : 스타일에 설정된 이미지나 글꼴 파일을 문자열 형태의 데이터(Base64)로 변환하여 해당 CSS 파일 안에 포함시켜버리기 때문에 정적 파일을 관리하기 쉬워진다. 하지만 실제 파일들보다 용량이 커지고, CSS 파일이 무거워지므로 적당히 사용하는 것을 권장한다.
     - file-loader : 정적 파일을 로드하는데 사용되며, 보통 프로젝트 환경에 맞게 특정 디렉토리에 파일을 복사하는 역할을 수행한다.
     
### 2. WebPack App
  - webpack setting
    - root directory에 'webpack.config.js' file 생성
    - webpack.config.js 작성(파일 참조)
     ~~~
     Source 경로: module.exports = {context: path.resolve(__dirname, 'Source 경로') ... }
     Build SRC: module.exports = { ... entry: {Build SRC entry: '경로'} ... }
     Build DST: module.exports = { ... output: {path: __dirname, filename: '경로'} ... }
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


