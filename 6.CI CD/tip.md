# TIP
---


# GitHub
## 1. 대용량 파일 upload(100MB 이상)
### 1. git-lfs 설치
  - https://git-lfs.github.com/
  
### 2. git-lfs 적용
  ```console
  [root@repository ~]# git lfs install
  [root@repository ~]# git lfs track "올릴파일.확장자"
  [root@repository ~]# git commit -m "comment"
  ```

### 3. BFG Repo-Cleaner
  - 기존에 100MB 보다 큰 파일의 로그를 강제로 삭제
  - jar download
    -https://rtyley.github.io/bfg-repo-cleaner/
    
  - download 된 jar file을 이용하여 아래 명령어 수행
  ```console
  [root@repository ~]# java -jar bfg-x.x.x.jar --strip-blobs-bigger-than 100M

  또는

  [root@repository ~]# git repack && git gc
  [root@repository ~]# java -jar bfg-x.x.x.jar --strip-blobs-bigger-than 100M
  ```

---


# Jenkins
  - 소프트웨어 개발 시 지속적으로 통합 서비스를 제공하는 툴이다. CI(Continuous Integration) 툴 이라고 표현한다.
  
  - 참고사이트: [https://www.jenkins.io/doc/book/pipeline/](https://www.jenkins.io/doc/book/pipeline/)
  
## 1. ????? 

## 2. Jenkins Pipeline
  - Jenkins Pipeline 이란 스크립트를 통해 파이프라인의 흐름을 정의하는 기능
  - Architecture
    - Node
    - Master: Jenkins에 모든 설정과 권한을 갖고 있으며, Agent를 관리하고 Job을 실행토록 Agent에게 Job을 할당
    - Agent(=과거 Slave): Job을 실행할 수 있는 서버
    - Executor: Node에서 Job을 실행시키는 모듈
    - Label: tag 비슷한 역활
    - Stage: DSL 명령어를 수행
    - Step: Stage안에 있는 DSL명령어들
    
    - 참고사이트: [https://www.jenkins.io/doc/book/pipeline/syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
  
### 1. parallel
  
#### 1. Declaractive Pipeline
  - Grovy-syntax기반
  ```console
  pipeline {
    agent none      //Backgroud로 돌아가는 agent를 정의, 해당 pipeline은 다른 job을 실행 하는 것 이기 때문에, agent불필요
    stages {
      stage('1st_STAGE_NAME') {
        parallel {
          stage('1st_STAGE_JOB_NAME') {
          }
          stage('2nd_STAGE_JOB_NAME') {
          }
          stage('3th_STAGE_JOB_NAME') {
          }
        }
      }
      stage('2nd_JOB_NAME') {
      }
      // 1st_STAGE_NAME -> 2nd_STAGE_NAME 순으로 진행
    }
  }
  ```

#### 2. Scripted Pipeline
  - Grovy 기반(Grovy 문법이 선행되어야 하며, 진입장벽이 높다.)
  - Declaractive보다 효과적으로 많은 기능을 포함하여 작성 가능
  ```console
  node {
    stage('Parallel-test') {
      parallel 'Build-test-1' : {
        build job : 'Build-test-1'
      }, 'Build-test-2' : {
        build job : 'Build-test-2'
      } , 'Build-test-3' : {
        build job : 'Build-test-3'
    }
  }
  stage('Build-test-4') {
     build job : 'Build-test-4'
  }
}
  ```

#### 3. Combine A and B
  - Declaractive Pipeline과 Scripted Pipeline를 혼합하여 사용가능
  ```console
  node {
    stage('Parallel-test') {
        parallel 'Build-test-1' : {
            build job : 'Build-test-1'
        } , 'Build-test-2' : {
            build job : 'Build-test-2'
        } , 'Build-test-3' : {
            build job : 'Build-test-3'
        }
    }
  }
  pipeline {
      agent none 
      stages {
          stage('Build-test-4') {
              steps {
                  build 'Build-test-4'
              }
          }
      }
  }
  ```