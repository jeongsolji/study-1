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
    
##### 2. Amazon EBS ( = SAN )
  - 내가 만든 인스턴스 한대의 전용으로 사용하는 Storage
  - 데이터 저장공간(Volume)에 대한 요금을 측정하지, 실제로 저장된 데이터의 양으로 요금이 산정되지 않는다.
  - 주로 장기간 사용하지 않을 경우 back-up본을 만들어 저장을 시켜놓는다.
  - 인스턴스 하나당 EBS volume 하나를 두는걸 권고한다.
    
###### 1. 기본 정보
  - 비용
    - EBS는 인스턴스를 만드는 순간부터 비용이 측정된다.
      
##### 3. Amazon Ec2 Instance Storage [ 002 - : 03: 48]

##### 4. Amazon EFS/FSx ( = NAS ) [ 2일차 002 - 00: 41: 00] 
  - EFS : Linux 전용(NTFS File System)
  - FSx : Windows 전용

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

#### 2. EC2 (Elastic Compute Cloud)
##### 1. 기본정보
  - 사용자 데이터
    - 인스턴스기 만들어지면서 딱 한번 호출하는 데이터
  - 메타 데이터
    - 
  - key pari
    - 

#### 3. Database
  - 관계형 데이터베이스
    - Amazon RDS : 관리형 데이터베이스 서비스
      - 모니터링을 통해서 Instance Type의 변경이 필요한 정도를 제외하곤 거진 AWS에서 관리를 해준다.
      - 최근에는 Storage가 부족하면 Auto Scaling까지도 가능하다.
    - Amazon Redshift : 
    - Amazon Aurora : Amazon에서 개발한 Database(MySQL과 PostgreSQL과 호환이 가능하며, 어떤DB와 호환용으로 만들지 선택해야한다.)
      - 선택된 리전에 서로 다른 3곳의 가용영역에 2개씩 총 6개가 만들어진다.
        - 읽기전용, 장애대응용 DB가 있다.
  - 비관계형 데이터베이스
    - Amazon DynamoDB : 관리형 비관계형 데이터베이스
      - 계정 또는 리전이 달라지면, 동일한 Table Name 이더라도 다른 Table로 인식된다.
      - Global Table은 위의 상황에 대비해 어느 계정 어느 리전으로 접근해도 같은 Table로 이루어 질 수 있게 동기화된다.
      - 읽기, 쓰기 용량을 신경써야한다.(100ms 단위로 처리되지만, 초당 몇번 처리하게 할 것인지 제한을 걸 수 있다.)
      - 접근제어
        - DB자체를 관리자가 직접 컨트롤 하는게 아니기 때문에, IAM으로 서비스의 접근을 제한한다.
    - Amazon ElastiCache : 관계형 데이터베이스 앞단에 성능을 높히기 위하여 사용(샤딩 등)
    - Amazon Neptune : 그래프 데이터를 저장하는데 최적화

#### 3. VPC
  - organic제이션스?!: 계정이 여러개라도 통합계정처럼 사용가능
  - VPC를 만들면 default 라우팅이 함께 만들어진다.
    - default 라우팅은 아래와 같이 되어있기때문에 모든 통신이되고, NACL(Network Access Controll List)로 접근하는 방법밖에 차단할 수 있는 방법이 없다.   
    |목적지        |        대상|   
    |--------------------------|   
    |vpnip/cidr   |      local     
  - subnet을 만들면, default 라우팅이 기본적으로 적용된다.
  
  - 퍼블릭 서브넷
    - 인터넷에서 먼저 접근이 가능
  - 프라이빗 서브넷
    - 인터넷에서 먼저 접근이 불가
  
  - VPC간에 연결을 위해 Trasinc Gageway를 사용한다.
  
  - vpc end point
    - instance와 AWS Resource(S3, DanamoDB 등)과 통신 시, internet을 통하지 않고 통신할 수 있게 하는 것.
    - 두 가지 유형의 앤드포인트
      - interface endpoint
        - VPC밖에 있는 Resource들을 VPC안에 있는 것 처럼
      - gateway endpoint
      
#### 4. Load Balancing(ELB)
  - TLS(SSL) 가속기 역활까지 한다.
  - 종류
    - ALB
    - NLB
    - CLB
    
#### 5. Route 53
  - 서로 다른 vpc나 서로 다른 resion에 대한 통신을 구현할 때 사용
  - DNS 서비스를 지칭
    - 
    
  - 라운드 로빈
    - 레코드 수만큼 로드밸런싱(2개의 서버의 가중치 기반 라운드로빈을 5:5로 주었다고 생각하면됨.)
  - 가중치 기반 라운드 로빈
    - 어떤 주소로 어떤 비율로 줄꺼냐
  - 지연 시간 기반 라우팅
  - 상태 확인 및 DNS 장애 조치
  - 지리 위치 라우팅
    - 위도경도를 보고 가장 근접한 server로 라우팅
  - 트래픽 바이어스를 통한 지리 근접 라우팅
  - 다중 값 응답
  
#### 6. 사용자
  - 계정(Account)
  - 사용자(User)
  - 정책
    - AWS 서비스에 대한 엑세스를 제어
    - OS, Application에 대한 엑세스 제어를 하지 못함
    - 거부 > 허용 순으로 확인된다.
  - 역할
    - 자격증명을 발급해주는데, 유strategy효기간이 있는 자격증명을 발급해줄 때 사용.
    - 자격증명과 정책을 연결하여서 사용자에게 부여한다.
  
  - 추천: 아무런 권한이 없는 계정을 하나 만들고, 역활전환을 한 후에 cli연동을 진행한다.

### 7. System Manager
  - Ansible에 상응하는 서비스
  
#### 8. Amazon SQS
  - 1개 메시지당 256kb밖에 전달 할 수 없다. 이때는 S3에 데이터를 올려 두고, S3의 링크를 전달 해 주는 방법으로 해결할 수 있다.

#### 9. 자격증
  - https://aws.amazon.com/ko/certification
  
  - http://bit.ly/aws-study-resource
  - http://bit.ly/sacertguide
  - https://www.examtopics.com/exams/amazon
    - https://www.examtopics.com/exams/amazon/aws-certified-solutions-architect-associate-saa-c02/
    - 위의 문제만 잘 이해해도 자격증은 취득할 수 있다.

--- 


### 2. 2일차
#### 1. 요금 [ 2일차 002 - 00: 53: 00] 
  - 온디멘드
    - 개발 및 테스트기간
  - 예약인스턴스
    - 이빨만 잘 털면 최대 75% 할인이 가능하다.


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


cloudwatch -> container monitoring
https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/Container-Insights-metrics-EKS.html

2일차 : 실습
  - https://distribute99.s3.ap-northeast-2.amazonaws.com/code_dist/index.html
  - zlagusdbs@shinsegae.com / aws2020
  - http://do-not-delete-security-builders-201-lab-contents.s3-website.ap-northeast-2.amazonaws.com/lab/


---



## 99. Calc
  * AWS Calc 참고사이트: [calculator.aws](calculator.aws)

  * IP Calc 참고사이트: [http://jodies.de/ipcalc](http://jodies.de/ipcalc)
