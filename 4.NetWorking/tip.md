# TIP
---

# NetWork
## 1. OSI 7 Layer / TCP/IP
  - OSI 7 Layer [ 국제표준 ]
    - 국제표준화기구(ISO)에서 네트워크 통신에서 일어나는 과정을 7단계로 나누어 정의한 모델

  - TCP/IP [ 산업표준 ]
    - OSI 7 Layer의 4Layer(전송계층)를 TCP로 사용하고, 3Layer(네트워크계층)를 IP로 고정시킨 뒤, 복잡한 7계층을 4계층으로 함축시켜 놓은 모델

  -> 그림1. OSI7Layer-TCPIP
---


# Port Forwarding
## 1. Windows
- 목록확인: netsh interface portproxy show v4tov4
- 목록추가: netsh interface portproxy add v4tov4 listenport=8022 listenaddress=127.0.0.1 connectport=22 connectaddress=192.168.100.1
- 목록삭제: netsh interface portproxy delete v4tov4 listenport=8022 listenaddress=127.0.0.1
