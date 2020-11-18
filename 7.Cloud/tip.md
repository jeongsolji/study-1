# TIP
---


# AWS
## 1. Education
### 1. 1일차
#### 1. Storage
##### 1. Amazon S3
  - 인터넷용 스토리지
  - 온라인, HTTP Method 기반 Access

###### 1. 기본 정보
  - Bucket : 물리적 저장단위(S3는 Service Name 자체를 뜻한다.)
  - 비용
    - 데이터 량
    - 요청에 대한 비용(HTTP Method)
    - 데이터 전송비용
    
###### 2. 보안

###### 3. 버전관리
  - 성능 저하 없이 실수로 덮어쓰거나 삭제하는 것을 방지
  - 모든 업로드에 대해 새 버전을 생성합니다.
  - 요금
    - 버전관리를 위한 요금은 측정되지 않으나, 버전관리로 인한 객체(주로 파일)들이 쌓이면서 요금이 과금될 수 있다.

###### 4. 수명주기

###### 5. Storage Class
  (요청이 많은 경우 유리)
  - Amazon S3 standard
  - Amazon S3 standard - Infrequent Access
  - Amazon S3 One Zone-Infrequent Access
  (요청이 적을 경우 유리)
  
  - Amazon S3 Glacier
    - 장기 아카이빙 저장 서비스로써, 오랜 기간 자주 엑세스 하지 않는 서비스에 적합하다.
    - 데이터 사용 시 복원이 비싸다.
  
  - 가용성에 차이가 있다. 하향일 수록 가용성이 떨어진다.
    
##### 2. Amazon EBS
  - 내가 만든 인스턴스 한대의 전용으로 사용하는 Storage
  - 데이터 저장공간(Volume)에 대한 요금을 측정하지, 실제로 저장된 데이터의 양으로 요금이 산정되지 않는다.
  - 주로 장기간 사용하지 않을 경우 back-up본을 만들어 저장을 시켜놓는다.
    
###### 1. 기본 정보
  - 비용
    - EBS는 인스턴스를 만드는 순간부터 비용이 측정된다.
      
##### 3. Amazon Ec2 Instance Storage [ 002 - : 03: 48]

###### 3. IAM
  - 역할 : 유효기간이 있는 자격증명
    
##### 5. 탄력성(Auto Scaling) [ 00: 46: 00 ]
  - Infra가 자체적으로 줄이고 늘리는 작업을 뜻 한다.
    
###### 1. 구성요소
  - ELB(Elastic Load Balancing) [ 00: 51: 00]
    - CLB : VPC 이전에 EC2-Classic 네트워크라고 있었는데, 그 당시에 같이 사용하던 ELB
    - ALB : 7계층 Load Balancer
      - 외부에서 내부로 트래픽 분산을 하는 용도로 사용
    - NLB : 4계층 Load Balancer
      - 내부에서 내부로 트래픽 분산을 하는 용도로 사용
  - Auto Scaling
    - 내결함성 향상 : (Instance에 Host가 없어지는(장애) 경우, 총 인스턴스의 갯수를 지정해 놓고 그 갯수 이하로 instance가 줄어든다면 자동으로 늘려줄 수 있다.
    - 시작구성 : Auto Scaling에서만 사용하는 탬플릿 
    - 시작탬플릿 : Auto Scaling 또는 그 외 사용가능한 탬플릿, 시작구성에 비해 versioning이 가능함.
  - Amazon CloudWatch
    - Cloud 환경에 Resource를 Monitoring, Triggering 할 수 있다.

### 2. 2일차
  - 

---


## 2. AWS-CLI
  - 참고사이트: [https://docs.aws.amazon.com/cli/index.html](https://docs.aws.amazon.com/cli/index.html)
  
### 1. Summary
  - AWS Command Line Interface(AWS CLI)는 명령줄 셸의 명령을 사용하여 AWS 서비스와 상호 작용할 수 있는 오픈 소스 도구입니다. 최소한의 구성으로 AWS CLI를 사용하면 원하는 터미널 프로그램에 있는 명령 프롬프트에서 브라우저 기반 AWS Management 콘솔에서 제공하는 것과 동일한 기능을 구현하는 명령을 실행할 수 있습니다.
  - Version
    - version 2.x
    - version 1.x
  
### 2. Install
  - 4가지 방법: docker, linux, macOS, windows
  
  * 참고사이트: [https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/install-cliv2.html](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/install-cliv2.html)


---



## 99. Calc
  * 참고사이트: [calculator.aws](calculator.aws)
