# Shell

---


# 1. Shell
  - User와 Kernel간의 인터페이스 역할을 하는 모듈(ex> 명령어 해석 기능 등)
  - Shell의 종류
    - Bourne Shell: 유닉스의 표준 쉘
    - Bash Shell: Linux 표준 쉘
    - C Shell
    - Korn Shell
  - Shell의 확인
  ```console
  [root@localhost ~]# echo $SHELL
  
  또는
  
  [root@localhost ~]# evn | grep SHELL
  oracle:x:1021:1020:Oracle user:/data/network/oracle:/bin/bash
  1      2 3    4    5           6                    7
  --------------------------------------------
- Result
    1 | Username: It is used when user logs in. It should be between 1 and 32 characters in length.
    2 | Password: An x character indicates that encrypted password is stored in /etc/shadow file. Please note that you need to use the passwd command to computes the hash of a password typed at the CLI or to store/update the hash of the password in /etc/shadow file.
    3 | User ID (UID): Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs 1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for administrative and system accounts/groups.
    4 | Group ID (GID): The primary group ID (stored in /etc/group file)
    5 | User ID Info: The comment field. It allow you to add extra information about the users such as user’s full name, phone number etc. This field use by finger command.
    6 | Home directory: The absolute path to the directory the user will be in when they log in. If this directory does not exists then users directory becomes /
    7 | Command/shell: The absolute path of a command or shell (/bin/bash). Typically, this is a shell. Please note that it does not have to be a shell.
  - Description
      - Shell 확인
  ```
  
## 1. Tech
### 1. 큰따옴표와 작은따옴표
    - 셸 스크립트에서 모든 변수값을 문자열이라 기본으로 인식하기 때문에 큰 따옴표가 딱히 필요가 없습니다.
    - 그룹핑에 목적으로 많이씀
    - 큰따옴표는 특수문자를 보존시켜주는데 ""보다 ''가 더 강력하게 보호해줍니다. 예를 들어 '$'라는 문자가 들어간 글자를 출력하려면 큰따옴표가 아닌 작은 따옴표로 묶어줘야한다. 요런 약간의 차이가 있지만 일반적으로는 동일하게 인식합니다.
    ```console
    [root@localhost ~]# name=kim
    [root@localhost ~]# $name
    kim
    
    [root@localhost ~]# name=\kim
    [root@localhost ~]# $name
    kim     //역슬래쉬가 빠짐
    
    [root@localhost ~]# name=\kim
    [root@localhost ~]# "$name"
    \kim     //역슬래쉬가 빠지지 않음
    
    
    [root@localhost ~]# name=\kim
    [root@localhost ~]# "$name"
    \kim
    [root@localhost ~]# name=\kim
    [root@localhost ~]# '$name'
    $name
    ``` 

### 2. 변수
    - 타입을 설정하지 않음
    - 대입연산자 좌우로 공백이 존재할 수 없음
    - 영문,숫자,언더바('_') 만 사용가능
    ```console
    variable_one=KimHyunYun    //correct
    variable_one =KimHyunYun    //incorrect
    variable_one= KimHyunYun    //incorrect
    ```
    
    - 사용
    ```console
    ${변수명}
    
    또는
    
    $변수명
    ```
