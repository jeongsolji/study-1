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
  - 참고사이트: [https://www.jenkins.io/doc/book/pipeline/](https://www.jenkins.io/doc/book/pipeline/)
  
## 1. 

## 2. Jenkins Pipeline
  - Jenkins Pipeline 이란 스크립트를 통해 파이프라인의 흐름을 정의하는 기능
  
### 1. Jenkins File
  - 

