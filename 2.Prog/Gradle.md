
---


# Gradle
## 사전지식
- build: ex> jar(compile + test + deploy + etc)
  - source code file을 컴퓨터에서 실행할 수 있는 독립 소프트웨어 가공물로 변환하는 과정을 말하거나 그에 대한 결과물을 얻는 과정

## DSL(Domain Sepecific Language)
- [https://docs.gradle.org/current/dsl/](https://docs.gradle.org/current/dsl/)
- DSL 활용
  - 비공개(Internal): 
  - 실험상태(Incubating): Test중으로 아직 배포되지 않은 상태
  - 공개상태(Public): 배포된 상태
  - 폐지상태(Deprecated): 배포를 중지하기로한 상태

## Gradle Life Cycle
- gradle life cycle 3단계
  ```
  initialization -> Configuration -> Execution
  ```
  - initialization(ref file: settings.gradle)
    - project name, 사용하는 모듈 등을 확인한다.
    - 멀티 모듈 프로젝트 일 때는 각 모듈 별로 build.gradle{.kts}가 있는지 확인한다.
  - congifuration(ref file: build.gradle{.kts})
    - buildScript의 라이브러리를 가져오거나(plugin 메소드), 프로젝트를 configure하는 작업(congiruations 메소드), 프로젝트에서 사용할 라이브러리를 가져오는 작업(dependencies 메소드) 등 을 수행한다.
    - 이후 Gradle Task를 순차적으로 수행한다. 단, Gradle Task의 기본 블록은 Configuration단계에서 수행되지만, doFirst{}, doLast{} 메소드를 이용하여 Execution 단계에서 수행되도록 할 수도 있다.
  - execution
    - build를 진행한다.(code gen(=annotation processing), compile, test, packaging 등)

## 구성요소
- 초기화 설정 스크립트(Initialization Setting Script): settings.gradle
- 빌드 구성 스크립트(Build Congifuration Script): gradle.build{.kts})
- 속성 파일(gradle.properteis)
- 환경변수/명령어 옵션
  - example> gradle clean compile
- 프로젝트 디렉터리(buildSrc): 빌드 수행 시, 클래스 파일이나 플러그인을 저장하여 참조하는 디렉터리

## How to use ?
### initialization(ref file: settings.gradle)

### congurations(ref file: build.gradle)
#### repositories 메소드
- 저장소를 설정을 담당.
- RepositoryHandler를 통해 실행.

#### ext 메소드
- buildScript에서 전역변수로 사용하기 위해 사용.

#### plugins 메소드
- plugin을 등록하면, 해당 플러그인에 포함된 수많은 Task들이 Gradle파일로 들어온다.
```
# Intellij의 gradle 탭을 보면 spring관련된 task들이 보일것이다(bootRun 등)
plugins {
    id 'org.springframework.boot' version 'x.x.x'
}
```

#### configurations 메소드
- build.gradle 내부에서 사용되는 설정을 정의한다.

#### dependencies 메소드
- compileOnly
- runtimeOnly
- api(Deprecated compile)
  - Parent Hierarchy 구조 일 때, 모든 Parent의 의존성을 노출한다.
  - [https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations](https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations)
  ```
  Gradle은 컴파일 클래스 경로와 빌드 출력에 종속 항목을 추가합니다.
  모듈에 api 종속 항목이 포함되면 모듈이 다른 모듈로 종속 항목을 이전하여 다른 모듈에서 런타임과 컴파일 시간에 사용할 수 있도록 한다는 것을 Gradle에 알려주는 것입니다.
  이 구성은 compile(현재는 지원 중단됨)처럼 작동하지만 주의해서 다른 업스트림 소비자에게 이전해야 하는 종속 항목과만 사용해야 합니다.
  그 이유는 api 종속 항목이 외부 API를 변경하면 Gradle이 컴파일 시 종속 항목에 액세스 권한이 있는 모든 모듈을 다시 컴파일하기 때문입니다.
  따라서 api 종속 항목 수가 많으면 빌드 시간이 크게 증가할 수 있습니다.
  종속 항목의 API를 별도의 모듈에 노출하지 않으려면 라이브러리 모듈에서 implementation 종속 항목을 대신 사용해야 합니다.
  ```
- implementation
  - Parent Hierarchy 구조 일 때, 직계 Parent의 의존성을 노출한다.
  - [https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations](https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations)
  ```
  Gradle이 컴파일 클래스 경로에 종속 항목을 추가하고 빌드 출력에 종속 항목을 패키징합니다.
  그러나 모듈에서 implementation 종속 항목을 구성하면 모듈이 컴파일 시간에 종속 항목을 다른 모듈에 누출하기를 바라지 않는다는 것을 Gradle에 알려주는 것입니다.
  즉 종속 항목은 런타임에만 다른 모듈에서 이용할 수 있습니다.
  api 또는 compile(지원 중단됨) 대신 이 종속 항목 구성을 사용하면 빌드 시스템에서 다시 컴파일해야 하는 모듈 수가 줄어들기 때문에 빌드 시간이 크게 개선될 수 있습니다.
  예를 들어 implementation 종속 항목이 API를 변경하면 Gradle은 이 종속 항목과 이에 직접적으로 종속된 모듈만 다시 컴파일합니다.
  대부분의 앱과 테스트 모듈은 이 구성을 사용해야 합니다.
  ```
- testImplementation
  - Test 시에 의존성을 노출한다.
- annotationProcessor
  - annotation processor 명시
  ```
  dependencies {
      annotationProcessor 'org.projectlombok:lombok'
  }
  ```
  

  
#### Task 메소드
- Gardle의 실행 작업 단위
```
task 테스크이름1 {
    // TODO
}

task 테스크이름2(dependsOn:['테스크이름3', '테스트이름4' ... ]) {
}
```
  - dependsOn
    - Task들 사이의 의존성을 지정

---

# Ref
- [https://www.youtube.com/watch?v=hbZJPhceVg4&list=PL7mmuO705dG2pdxCYCCJeAgOeuQN1seZz&index=13](https://www.youtube.com/watch?v=hbZJPhceVg4&list=PL7mmuO705dG2pdxCYCCJeAgOeuQN1seZz&index=13)

