# Gradle

---


# Gardle 이란
## 사전지식
- 빌드: 소스 코드 파일을 컴퓨터에서 실행할 수 있는 독립 소프트웨어 가공물로 변환하는 과정을 말하거나 그에 대한 결과물을 얻는 과정(Compile, Test, Deploy 등)

## DSL(Domain Sepecific Language)
- [https://docs.gradle.org/current/dsl/](https://docs.gradle.org/current/dsl/)
- DSL 활용
  - 비공개(Internal): 
  - 실험상태(Incubating): Test중으로 아직 배포되지 않은 상태
  - 공개상태(Public): 배포된 상태
  - 폐지상태(Deprecated): 배포를 중지하기로한 상태

# Gradle Build
- 구성요소
  - 초기화 스크립트(init.gradle): 기본객체='Gradle'
  - 설정 스크립트(settings.gradle): 기본객체=Settings
  - 빌드 스크립트(gradle.build): 기본객체=Project
  - 속성파일(gradle.properteis)
  - 환경변수/명령어 옵션
    - example> gradle clean compile
  - 프로젝트 디렉터리(buildSrc): 빌드 수행 시, 클래스 파일이나 플러그인을 저장하여 참조하는 디렉터리
- 빌드수행단계
  ```
  명령어 해석/수행 -1-> Gradle 실행 -2-> Script 초기화 -3-> Project Settings -4-> Task 실행 -5-> 결과출력
  
  // 1. 명령어 해석, 디렉터리 확인, 속성파일 확인, 클래스 인스턴스 생성, 실행모두 판단(데몬, GUI, CLI)
  // Gradle Life Cycle(2, 3, 4)
  // 2. Script File 확인 및 읽기, 멀티or싱글 프로젝트 판단, Project 객체생성, 명령어 옵션 및 인수설정
  // 3. 참조 중인 라이브러리 확인, Task 객체 및 Task Graph 생성
  // 4. Task 추출, Task 실행
  ```

# Gradle Script File
- [http://groovy-lang.org/](http://groovy-lang.org/)
- 구성요소
  - 처리문
  - 스크립트 블록
    - repositories: 저장소 설정
    - dependencies: 의존관계설정
    - buildscript: 빌드 스크립트 클래스 패스 설정
    - initscript: 초기화 스크립트 설정
    - configurations: 환경 구성 설정
    - allprojects: 서브 프로젝트 포함 전체 프로젝트 설정
    - subprojects: 서브  프로젝트 설정
    - artifacts: 빌드 결과에 대한 설정
    
- 변수
  - 지역변수: 선언된 부분에서 영향력 있는 변수.(Gradle의 모든 Script File에서 사용가능)
    - 'def' 키워드를 사용하여 선언
  - 시스템 속성: 시스템 관련 정보를 저장하는 변수.(Gradle의 모든 Script File에서 사용가능)
    - 명령어 실행 시 인수(-P 또는 -project-prop 옵션)를 기재하거나, 속성파일(gradle.properteis)안에 선언
  - 확장 속성: 도메인 객체 확장 용도로 사용되는 변수..(Gradle의 모든 Script File에서 사용가능)
    - 'ext' 키워드를 사용하여 선언
  - 프로젝트 속성: 프로젝트에서 사용되는 변수.(빌드 스크립트안에서만 사용가능)
  
# Gradle 객체
## Project 객체
- [https://www.youtube.com/watch?v=hbZJPhceVg4&list=PL7mmuO705dG2pdxCYCCJeAgOeuQN1seZz&index=13](https://www.youtube.com/watch?v=hbZJPhceVg4&list=PL7mmuO705dG2pdxCYCCJeAgOeuQN1seZz&index=13)

---


# Build Script
## 
- api(구 compile)
  - Parent Hierarchy 구조 일 때, 모든 Parent의 의존성을 노출한다.
  - 위의 사유로 
- implementation
  - Parent Hierarchy 구조 일 때, 직계 Parent의 의존성을 노출한다.
