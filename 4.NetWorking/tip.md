# TIP
---


# Prot Forwarding
## 1. Windows
- 목록확인: netsh interface portproxy show v4tov4
- 목록추가: netsh interface portproxy add v4tov4 listenport=8022 listenaddress=127.0.0.1 connectport=22 connectaddress=192.168.100.1
- 목록삭제: netsh interface portproxy delete v4tov4 listenport=8022 listenaddress=127.0.0.1
