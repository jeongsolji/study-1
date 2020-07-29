# TIP
---


# Infra
## 1. Docker
### 1. Docker 란
  - Hypervisor
    - 기존의 가상화 기술로써 가상머신으로 구분하여 운영체제를 관리하기 때문에, Kernel, OS lv의 lib 등 불필요한 기능을 많이 내포하기 때문에 성능에 이슈가 존재한다.
  - Docker
    - Hypervisor의 성능이슈 해결(새로운 OS에 대한 lib, env등을 생략할 수 있음)
    - Linux 자체기능인 chroot, cgroup, namespace 등을 사용하여, 표면적으로 독립된 가상화된 공간을 만들어 관리 할 수 있게 하는 기술(Client, Server(≒Engine))
      - chroot: root directory인 '/'를 변경하는 명령어
      - cgroup: Linux Kernel에서 CPU, Memory, DISK, Network 등의 자원을 할당하는 가상화 공간을 제공하는 명령어
      - namespace: Process tree, 사용자계정, file system, ipc 등을 묶어 HOST와 완벽하게 격리시킨 공간
  - Container
    - 표면적으로는 OS자체를 가상화하였으나, 내부적으로는 OS자체를 가상화하지 않은형태(Linux자체 기능인 chroot, namespace, cgroup을 사용하여 프로세스 단위의 격리 환경을 만들어 놓은 곳)이기 때문에 Container라 지칭

#### 1. Dokcer [ 관리단위 : Container ]
  - Docker 위치 확인
  ```console
  # which docker
  ```

  - Docker 실행위치 확인
  ```console
  # ps aux | grep docker
  ```

  - Docker Version 확인
  ```console
  # docker -v
  Dokcer version xx, build xxx
  ```

#### 2. Docker Image [ {저장소이름}/이미지이름:이미지버전 ]
  - Docker Container를 만들기 위한 정보로써, OS 및 Application의 정보를 하나의 정보로 묶어서 관리되는 파일을 뜻한다.

#### 3. Docker HUB
  - Docker Image의 원격저장소(≒ Git HUB)
  
#### 4. Docker Container
  - 변천사: chroot으로 격리된 공간사용 -> LXC(LinuX Container) 도입 -> Docker 도입
    - chroot: directory만 구분하여 격리된 공간사용.
              설정이 매우 복잡.
    - LXC: group, namespace등을 활용하여, 일종의 Container(OS자체를 가상화하지 않기 때문에 Container라고 명명)
           실제 서비스를 운영하기에는 기능이 부족하였고, 추가하기도 까다로움
    - Docker: LXC를 보안하여 만든 것.
    
  - 하나의 Docker Container에는 하나의 Process만 구동시키는 것을 권장
    - ex> 'Docker Container for Web-Server' , 'Docker Container for database' 이렇게 2개의 Docker Container를 구동
    
#### 5. Docker Volume
  - Docker Container의 데이터를 영속적(Persistent)데이터로 활용
  
  - Docker Volume은 3가지 방법을 지원한다.
    - Host Sharing
    - Host -> Volume Docker Container -> Docker Container
    - Docker Volume [권장]
      - Docker 자체적으로 관리하는 Volume을 사용
      
#### 6. Docker Network
  - Docker Network Architecture
    - ![Database Server Setting](./../img/Docker-Network.png)

  - Docker Network Driver
    - Bridge
    - Host
    - None
    - Container
    - Overlay
  - 3th-party Network Driver(≒PlugIn)
    - weave
    - flannel
    - openvswitch

#### 7. Dockerfile
  - Docker의 Build를 실행 시, 설정정보를 기재하는 파일
  
  - 일반적인 Docker Image만드는 절차
    - [기본 OS Docker Image 다운] -> [Docker Container화] -> [Application 설치 및 셋팅] -> [Docker Container를 Docker Image로 변경]
  - 위의 절차를 하나의 파일로 기재하여, 자동으로 Docker Image를 만드는 행위를 Docker Build라고 하며, 이때 사용하는 File을 Dockerfile이라 한다.

### 2. Command Line Interface
  - Docker Engine
  ```console
  [root@localhost ~]# docker -v
  --------------------------------------
  - Command
      - docker -v
  - Description
      - Docker Engine의 버전을 확인


  [root@localhost ~]# docker inspect web-server
  --------------------------------------
  - Command
      - docker inspect [CONTAINER, IMAGE ...]
  - Option
      --type: 조회 정보를 명시
              ex> docker inspect --type image
                  - Docker Image만 조회
  - Description
      - Docker가 관리하는 Container, Images, Volume 등 Docker의 모든 구성 단위를 조회
  ```


  - Docker Image
  ```console
  [root@localhost ~]# docker images
  --------------------------------------
  - Command
      - docker images
  - Description
      - Local Docker Engine에 존재하는 Docker Images 조회


  [root@localhost ~]# docker pull centos:7
  --------------------------------------
  - Command
      - docker pull IMAGE
  - Description
      - Docker HUB에서 해당 Docker Image를 Download


  [root@localhost ~]# docker rmi web-server
  --------------------------------------
  - Command
      - docker rmi IMAGE
  -f : (Force) 강제삭제
       Docker Container로 사용중인 Docker Image를 -f 옵션으로 삭제했다면, 이름만 none으로 변경된 것(이런 Docker Image를 Dangling Image라고 부름)이며 삭제되지는 않는다.
  - Description
      - Docker Image를 삭제하나, Container가 종료되고 나서 실행하여야 한다.


  [root@localhost ~]# docker image prune
  --------------------------------------
  - Command
      - docker image prune
  - Description
      - dangling image들을 한번에 삭제한다.


  [root@localhost ~]# docker commit \
                      -a "zlagusdbs" \
                      -m "my first commit" \
                      web-server \
                      web-server:first
  --------------------------------------
  - Command
      - docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  - Option
      -a : (Author) 작성자를 나타내는 메타데이터를 이미지에 포함
      -m : (Message) Commit Message를 뜻하며, 이미지에 포함될 부가설명이 된다
  - Description
      - web-server라는 Docker Container를 web-server:first라는 이름의 이미지로 생성


  [root@localhost ~]# docker save -o centos.tar centos:7
  --------------------------------------
  - Command
      - docker save [OPTION] FILE_NAME IMAGE
  - Option
      -o : (Output)extract 할 파일명을 입력
  - Description
      - Docker Container의 커맨드, 이미지 이름, 태그 등 이미지의 모든 메타 데이터를 포함하여 하나의 파일로 추출


  [root@localhost ~]# docker load -i centos.tar
  --------------------------------------
  - Command
      - docker load [OPTION] FILE_NAME
  - Option
      -i : (Input)load 할 파일명을 입력
  - Description
      - Docker Container의 커맨드, 이미지 이름, 태그 등 이미지의 모든 메타 데이터를 포함하여 하나의 파일로 추출
  ```


  - Docker Container
  ```console
  [root@localhost ~]# docker ps
  --------------------------------------
  - Command
      - docker ps
  - Optaion
      -a : All Docker container 조회
      -q : Only Docker Container ID만 조회
  - Description
      - 옵션을 기재하지 않을 시, 실행중인 Docker Container를 조회


  [root@localhost ~]# docker create -i -t --name web-server centos:7
  --------------------------------------
  - Command
      - docker create IMAGE
  - Option
      -i : 
      -t : 
      --name : Docker Container의 이름을 지정
  - Description
      - IMAGE를 이용하여 Docker Container 생성
      - [docker pull:Docker Image가 없을 때] -> [docker create]


  [root@localhost ~]# docker run -i -t centos:7
  --------------------------------------
  - Command
      - docker run [Option] IMAGE
  - Option
      -i : 
      -t : 
      -p : Port forwarding을 위한 옵션이며, [SourcePort:DesticationPort] 또는 [SourceIP:SourcePort:DestinationPort]로 기재
           ex> docker run -i -t -p 10.222.52.114:80:8080
               HOST IP인 '10.222.52.114'의 '80' Port로 진입 시, Docker Container의 '8080'Port로 Port forwarding을 진행.
      -d : '-i -t' Option이 attach 가능한 상태로 진입하도록 한다면, '-d'옵션은 Detached Mod로 진입
      -e : Docker Container 내부에서 사용하게 될 Enviroment(환경변수)를 설정
      -v :
           1. Host Sharing
             - Host의 Directory를 Docker Container의 특정 Directory로 공유받는다(≒Volume Sharing)                       //[@Docker Volume]
               ex> docker run --name volume_docker_container -v /home/database:/var/lib/mysql                       //[호스트 공유 디렉토리]:[도커 컨테이너의 디렉토리]
           2. Docker Volume Sharing
             - Docker Volume을 사용한다.
               ex> docker run --name database-server -v docker_volume:/root/                                        //[Docker Vaolume의 이름]:[Docker Container의 공유 디렉토리]
      --volumes-from: '-v'옵션을 사용하는 Docker Container의 '-v'옵션 값에 해당하는 Directory를 공유받는다                  //[@Docker Volume]
                     ex> docker run --name database-server --volumes-from volume_docker_container                   //[@Docker Volume]
                         Host의 '/home/database'의 Directory를 volume_docker_container가 공유받고 있기 때문에, 공유받는 TargetDirectory를 동일한 경로로 다시 공유 받는다.  
      -link[deprecated] : 다른 Docker Container를 Alias를 지정한다.
                          cf> A Docker Container에서 B Docker Container로 접근하는 방법 중 NAT로 받은 IP를 사용하는 방법이 있는데, Docker Egine은 Docker Container를 시작할 때마다 내부IP를 순차적으로 재할당한다.(DHCP개념).
                              매번 변경되는 DockerContainer의 IP를 관리하지 않고, 다른 Docker Container의 이름을 Alias로 지정하여 관리한다.(Docker Container Name또한 변경에 가능성이 있기에, 직접쓰지 않는다.)
                              ex> docker run --name web-server -link database-server:db-srv
                                  db-srv라는 이름으로 database-server Docker Container에 접근할 수 있다.
                          cf> deprecated 옵션이며, Docker Bridge를 사용을 권장한다.
  - Description
      - 'centos:7'라는 Docker Image가 Local Docker Engine에 존재하지 않을 경우, Docker HUB에서 Docker Image를 Download받아 설치 후 Docker Container로 진입
      - [docker pull:Docker Image가 없을 때] -> [docker create] -> [docker start] -> [docker attach: -i -t Option을 사용했을 때]


  [root@localhost ~]# docker start web-server
  --------------------------------------
  - Command
      - docker start CONTAINER
  - Description
      - CONTAINER를 실행


  [root@localhost ~]# docker restart web-server
  --------------------------------------
  - Command
      - docker restart CONTAINER
  - Description
      - CONTAINER를 재실행


  [root@localhost ~]# docker attach web-server
  --------------------------------------
  - Command
      - docker attach CONTAINER
  - Description
      - CONTAINER으로 진입


  [root@container ~]:/# exit
  또는
  Ctrl + D
  --------------------------------------
  - Command
      - exit
  - Description
      - Docker Container를 종료하고, quit


  Ctrl + P, Q
  --------------------------------------
  - Description
      - Docker Container를 종료하지 않고, quit
  ```


  - Docker Volume
  ```console
  [root@localhost ~]# docker volume ls
  --------------------------------------
  - Command
      - docker volume ls
  - Description
      - Docker Volume 조회

  [root@localhost ~]# docker volume create  --name docker_volume
  --------------------------------------
  - Command
      - docker volume create
  - Option
      --name : 
  - Description
      - Docker Volume 생성
  ```


  - Docker Network
  ```console
  [root@container ~]:/# docker network ls
  --------------------------------------
  - Command
      - docker network ls
  - Description
      - Network 목록 조회
  ```


## 2. Docker Swarm
### 1. Dokcer Swarm [ 관리단위 : Service ]
  - 여러 대의 도커 서버를 하나의 클러스터로 만들어 Docker Container를 생성하는 여러 기능을 제공.
  - Swarm Class과 Swarm Mode로 나뉨
    - Swarm Class(Legacy)
      - Swarm Class은 여러 대의 서버를 하나의 지점에 사용하도록 단일 접근점을 제공하는것에 초점을 맞춤
      - Docker Version 1.6 이후 사용
      - 분산 코디네이션과 에이전트 등이 별도로 실행돼야 한다.
    - Swarm Mode
      - Swarm Mode는 MSA Container를 다루기 위한 클러스터링에 초점을 맞춤
      - Docker Version 1.12 이후 사용
      - 분산 코디네이션과 에이전트 등이 엔진에 내장돼어있다.
      
    cf> 분산 코디네이션(Distributed Coordinator)
      - 분산 코디네이션이란, 여러 개의 도커 서버를 하나의 클러스터로 구성하기위해 각종 정보를 저장하고 동기화를 관리하는 기술을 뜻함.
      - 또한, 클러스터에 영입할 새로운 서버의 발견(Service Discovery), 클러스터의 설정 저장, 동기화 등 을 위한 매니저를 뜻하기도 한다.
      - etcd, zookeeper, consul 등이 대표적이다.
  
  
#### 1. Swarm Classic
  - 생략(교재에서 생략되었다.)
  
  
#### 2. Swarm Mode
  - 구조
    - Manager Node
      - 기본적으로 Worker Node를 포함하며, Worker Node를 관리하기 위한 Docker Server
      - HA를 위하여, Raft Consensus 알고리즘을 사용.
    - Worker[1...N] Node
      - 실제로 Docker Container가 생성되고 관리되는 Docker Server
      
      
#### 3. Swarm Mode Service
  - Architecture
  ![Swarm Mode Architecture](./../img/Docker-Swarm.png)
  
  - Tasks and scheduling
  ![Swarm Mode Tasks and scheduling](./../img/Docker-Swarm_TaskAndScheduling.png)
  
  - Service
    - Swarm Mode에서의 제어 단위

  - Replica
    - Task들을 함께 묶은 정보
    
  - Task
    - Node 안에 몇가지 기능(제어를 당하기 위한기능)과 Docker Container를 채울수 있는 Slot의 묶은 단위를 뜻하며, 채워진(Swarm Mode를 위하여 인스턴스화 된) Docker Container를 뜻하기도 한다.


  - Service란
    - Swarm Mode에서 제어하는 단위
    - 명령어를 실행하면, Service에 묶인 모든 Docker Container들은 같은 명령어를 수행한다.
  - Task
    - Service내에 Docker Containier를 뜻한다.
  - Replica
    - Task들이 Manager Node 또는 Worker Node에 할당이 된 상태 


### 2. Command Line Interface
  - Swarm Mode (명령어의 제어 단위는 Docker Container)
  ```console
  [root@localhost ~]# docker info | grep Swarm
  --------------------------------------
  - Command
      - docker info
  - Description
      - Docker Engine의 Swarm Mode Cluster 정보를 확인


  [root@managernode ~]:/# docker node ls
  --------------------------------------
  - Command
      - docker node ls
  - Description
      - Swarm Cluster에 등록된 Node들을 조회


  [root@managernode ~]:/# docker swarm init --advertise-addr 192.168.0.100
  --------------------------------------
  - Command
      - docker swarm init
  - Options
      --advertise-addr: Docker Server가 Manager Node에 접근할 수 있도록 IP를 noti 
  - Description
      - Manager 역할을 할 서버에 Swarm Cluster를 시작


  [root@managernode ~]:/# docker swarm join --token SWMTKN-1-5...
  --------------------------------------
  - Command
      - docker swarm join
  - Options
      --token: 해당 Swarm Cluster에 추가하기 위한 비밀키
  - Description
      - 새로운 Worker Node를 해당 Swarm Cluster에 추가


  [root@managernode ~]:/# docker swarm join-token first-worker-node
  --------------------------------------
  - Command
      - docker swarm join-token NODE_NAME
  - Description
      - Node의 token을 확인


  [root@first-worker-node ~]:/# docker swarm leave  --force
  --------------------------------------
  - Command
      - docker swarm leaver
  - Options
      --force: Manager Node는 해당 옵션을 추가해야만 삭제할 수 있다.
               Manager Node를 삭제하면 해당 Manager Node에 저장돼 있던 Cluster 정보도 삭제된다.(주의!)
  - Description
      - Swarm Mode를 해제한다.


  [root@first-worker-node ~]:/# docker swarm rm
  --------------------------------------
  - Command
      - docker swarm rm
  - Description
      - 해제된 Swarm Mode를 삭제한다.


  [root@managernode ~]:/# docker swarm promote first-worker-node
  --------------------------------------
  - Command
      - docker swarm promote first-worker-node
  - Description
      - first-worker-node Worker Node를 Manager Node로 변경


  [root@managernode ~]:/# docker swarm demote first-worker-node
  --------------------------------------
  - Command
      - docker swarm demote first-worker-node
  - Description
      - first-worker-node Manager Node를 Worker Node로 변경
  ```


  - Swarm Mode Service
  ```console
  // K8S로 바로 넘어가며, 다루지 않음.
  ```


## 3. Docker Compose
  - 각기 다른 Application의 Docker Container를 하나의 Project처럼 다룰 수 있도록 묶을 수 있는 환경을 제공하는 것.
    ex> Web Server용 Docker Container와 DB Server용 Docker Container를 묶어서 기동시키고 down시킨다.
    
  - Docker Container의 설정이 정의도니 YAML 파일을 읽어 Docker Engine을 통해 Docker Container를 생성한다.
  
### 1. Command Line Interface
  - Origin CLI
  ```console
  [root@localhost ~]# docker run -d --name msql \
                      zlagusdbs/composetest:mysql \
                      mysqld

  [root@localhost ~]# docker run -d -p 80:80 \
                      --link mysql:db --name web \
                      zlagusdbs/composetest:web \
                      apachectl -DFOREGROUND
  ```

  - Docker Compose - docker-compose.yml
  ```console
  version: '3.0'
  services:
    web:
      image: zlagusdbs/composetest:web
      ports:
        - "80:80"
      links:
        - mysql:db
      command: apachectl -DFOREGROUND
    mysql:
      image: zlagusdbs/composetest:mysql
      command: mysqld
  ```

  - Docker Compose CLI
  ```console
  [root@localhost ~]# docker-compose up -d
  --------------------------------------
  - Command
      - docker-compose up
  - Options
      -d: 
  - Description
      - first-worker-node Manager Node를 Worker Node로 변경
  ```

  // K8S로 바로 넘어가며, 자세한 사항은 다루지 않음.

## 3. Kebernetes
  - Kebernetes는 모든 Resource를 Object로 관리
  - Object [ 상위 4개가 꼭 알아야 할 Object]
    - pod: container의 집합
    - replica set: pods을 관리하는 controller
    - service
    - deployment
    - service account
    - node
  
### 1. Tools
  - Install
    - minikube
    - K8S in Docker for MAC/Windows
    - kubespray
    - kubeadm(권장)
    - kops
    - EKS, GKE 등의 Managed Service
  - command
    - kubeadm: kubernetes를 설치하거나, master에 worker를 조인할 때 사용.
    - kubectl: master에서 worker로 일괄명령을 내릴 때
    - kubernetes-cni: Kubernetes의 Container간 통신을 위해, 네트워크를 연결해주는 명령어
    - kubelet: container의 생성, 삭제, master와 worker간의 통신 역할을 담당하는 Agent
    
#### 1. Install
  - install with kubeadm
    - 1. Kubernetes 저장소 추가
    ```console
    [root@localhost ~]# curl -s https://packages.cloud.google.com/apt/doc/apt-doc-apt-key.gpg | apt-key add -
                        cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
                        def http://apt.kubernetes.io/ kubernetes-xenial main
                        EOF
    --------------------------------------
    - Command
        - 
    ```
    
    - 2. kubeadm 설치
      - 2-1. Docker 설치
      ```console
      [root@localhost ~]# wget -q0- get.docker.com | sh
      --------------------------------------
      - Command
          - 
      ```
      
      - 2-2. Kubernetes 설치(최신버전)
      ```console
      [root@localhost ~]# apt-get install -y kubelet kubeadm kebectl kubernetes-cni
      --------------------------------------
      - Command
          - 
      ```
      
      // 2-3. Kubernetes 설치(특정버전)
      ```console
      [root@localhost ~]# apt-get install -y kubelet=1.13.5-00 kubeadm kubectl kubernetes-cni
      --------------------------------------
      - Command
          - 
      ```
      
    - 3. Kubernetes Cluster Initialization
    ```console
    [root@kubernetes-master ~]# kubeadm init --kubernetes-version 1.13.5
                                --apiserver-advertise-address 172.31.0.100 \
                                --pod-network-cidr=192.168.0.0/16
      --------------------------------------
      - Command
          - 
      - Options
          --kubernetes-version : kubernetes의 특정버전을 설치(kubelet을 특정버전으로 설치 후, version을 맞출 때 사용)
          --apiserver-advertise-address : 다른 노드가 마스터에 접근할 수 있는 IP주소를 기재
                                        : 예> 다른 노드가 kube-master호스트에 접근할 수 있는 IP주소가 172.31.0.100
          --pod-network-cidr : kubernetes에서 사용할 컨테이너의 네트워크 대역
                             : 192.168.0.0/16은 calico.yaml의 기본 IP대역이다.(변경 시, 차후 calico.yaml내 대역을 변경해야 한다.)
      - Description
          - 
    ```
    
    - 4. kubernetes-master와 kubernetes-worker 들간의 결합
    ```console
    [root@kubernetes-worker1 ~]# kubeadm join 172.31.0.100:6443 --token aaa.bbb.ccc~~~
    
    [root@kubernetes-worker2 ~]# kubeadm join 172.31.0.100:6443 --token aaa.bbb.ccc~~~
    
    [root@kubernetes-worker3 ~]# kubeadm join 172.31.0.100:6443 --token aaa.bbb.ccc~~~
      --------------------------------------
      - Command
          - kubernetes-workerN 각각에 대해서, 위의 명령어를 실행하여 붙여준다.
    ```
#### 2. Network Addon(≒Network Plug-In)
  - Addon : 특정 프로그램의 기능을 보강하기 위해 추가된 프로그램
  - Network Addon : 네트워크연결을 보안하기 위한 프로그램
  - Kubernetes tools의 특장점만을 정리해 놓은 site
    - https://kubedex.com/kubernetes-network-plugins/
  - 종류
    - flannel
    - weaveNet
    - calico
    
  - install with calico
  ```console
  [root@kubernetes-master ~]# kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
  --------------------------------------
  - Command
      - 
  ```

  - install check
  ```console
  [root@kubernetes-master ~]# kubectl get pods --namespace kube-system
  --------------------------------------
  - Command
      - Kubernetes 핵심 컴포넌트들의 실행 목록을 확인

  [root@kubernetes-master ~]# kubectl get nodes
  --------------------------------------
  - Command
      - Kubernetes에 등록된 모든 node를 확인
  ```

### 2. Object
  ![kubernetes Architecture](./../img/Kubernetes Architecture.pdf.png)
  
#### 1. POD

#### 2. Replicat Set

#### 3. Deployment

#### 4. Service
  - 여러개의 Deployment를 하나의 완벽한 애플리케이션으로 연동할 수 있는 방법을 가능토록 한 Object
  - 즉, Deployment를 발견하고 Deployment들의 내부에 있는 POD들에 내부적으로 접근할 수 있도록 하는 Object
  
##### 1. 종류
  - ClusterIP
    - Kubernetes 내부에서만 POD들에 접근할 때 사용
    
  - NodePort
    - 외부에서 사용가능하지만, 모든 node의 특정 Port를 개방해 서비스에 접근하는 방식
    - Docker Swarm Mode에서 Container를 외부에 노출하는 방식과 같다고 보면된다.(운영에 적합하지 않음.)
    
  - LoadBalencer
    - LoadBalencer를 동적으로 생성하는 기능을 제공하는 환경(AWS, GCP 등 Cloud환경)에서만 사용가능.

#### 5. Ingress
  - 사전적: 외부에서 내부로 향하는 것을 지칭
  - K8S: 외부 요청을 어떻게 처리할 것인지 네트워크 7계층 레벨에서 정의하는 오브젝트
  
##### 1. 기능
  - 외부 요청의 라우팅: 특정 경로로 들어온 요청을 어떠한 서비스로 전달할지 정의하는 라우팅 규칙을 설정할 수 있음
  - 가상 호스트 기반의 요청 처리: 같은 IP에 대해 다른 도메인 이름으로 요청이 도착했을 때, 어떻게 처리할 것인지 정의
  - SSL/TLS 보안 연결 처리: 여러 개의 서비스로 요청을 라우팅할 때, 보안 연결을 위한 인증서를 적용
  - Ingress를 사용하지 않는 경우, Service를 통한 방법이 있는데, Service를 Deployment 수만큼 생성해야 한다. 이 자체는 Igress를 써도 동일하지만
    Service 각각에 설정을 하는 것을 Ingress를 통하여 통합적으로 관리할 수 있다.
    
##### 2. 구조
  - Ingress: 요청을 처리하는 규칙을 정의하는 선언적 오브젝트
  ```console
  [root@kubernetes-master ~]# kubectl get ingress
  --------------------------------------
  - Command
      - Kubernetes에 등록된 모든 Ingress를 확인

  [root@kubernetes-master ~]# kubectl apply -f ingress-example.yaml
  --------------------------------------
  - Command
      - 사전에 'ingress-example.yaml'을 생성 후 명령어 실행 시, 해당 정책을 반영한 ingress가 생성된다.
  ```
    
  - Ingress Controller Server: 실제 외부 요청을 받아들이며, Ingress 규칙을 로드해 사용.
    - NGinX, Kong 등 존재.
  ```console
  [root@kubernetes-master ~]# kubectl apply -f \
  https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
  --------------------------------------
  - Command
      - Ingres Controller를 설치
  - Options
      -f : NginX Ingress Controller는 Kubernetes에서 공식적으로 개발되고 있기 때문에, 설치를 위한 YAML 파일을 공식 깃허브 저장소에 직접 내려받을 수 있다.
  ```

#### 6. Persistent Volume / Persistent Volume Claim
  - Local Volume
    - hostPath: Host와 Volume을 공유
    - emptyDir: POD의 Container들 간에 Volume을 공유
    
  - Network Volume
    - On-Premise: NFS, iSCSI, GlusterFS, Ceph 와 같은 볼륨들이 존재
    - Cloud: EBS(Elastic Block Store), GCP(GcePersistentDisk) 와 같은 볼륨들이 존재
  
  - PV / PVC
    - POD가 Volume의 세부적인 사항을 몰라도 볼륨을 사용할 수 있도록 추상화해주는 역활을 담당.
    - 즉, POD를 생성하는 YAML입장에서 네트워크 볼륨이 NFS인지, AWS의 EBS인지 상관없이 볼륨을 사용할 수 있도록 하는 것이 핵심 아이디어.
      -> Volume의 YAML을 다른 곳에 배포할 때, Network Volume의 특정 볼륨을 선정해서 썻다면, 해당 YAML은 Network Volume의 특정 볼륨만 사용가능하다.

#### 7. Persistent Volume Claim


### 3. Command Line Interface
  - Object 확인
  ```console
  [root@kubernetes-master ~]# kubectl api-resources
  --------------------------------------
  - Command
      - Object들의 종류를 검색

  [root@kubernetes-master ~]# kubectl explain pod
  --------------------------------------
  - Command
      - 특정 object의 설명을 검색
  ```
