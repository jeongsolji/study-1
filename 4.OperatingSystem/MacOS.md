# MasOS

---

# Homebrew
## Homebrew 란?
  - macOS 용 Package 관리자
  - [참고사이트] [https://brew.sh/index_ko](https://brew.sh/index_ko)
  
### Hot to Install ?
  ```console
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```
  
### How to use ?
  ```console
  $ brew search zulu
  ==> Casks
  zulu11 ✔        zulu13       zulu15        zulu7        zulu8        homebrew/cask/zulu


  $ brew install zulu11
  ...


  $ brew list
  ==> Formulae
  bdw-gc		emacs		gnutls		libev		libtasn1	nettle		pcre2
  c-ares		gettext		guile		libevent	libtool		nghttp2		pkg-config
  cask		git		jansson		libffi		libunistring	openssl@1.1	readline
  coreutils	gmp		jemalloc	libidn2		m4		p11-kit		unbound

  ==> Casks
  zulu11
  ```
  
### Command
  |                         Command                        |               Describe               |              Blank              |
  |--------------------------------------------------------|------------------------------------- |---------------------------------|
  | brew install [패키지명]                                 | 패키지설치                            |                                 |
  | brew install [패키지명]                                 | 패키지설치                            |                                 |
  | brew remove --force --ignore-dependencies $(brew list) | Homebrew로 설치한 모든 패키지 일괄삭제  |                                 |
  | brew upgrade [패키지명]                                 | 패키지 Upgrade                        | 패키지명 미입력 시, 전체 업데이트 |
  | brew search [패키지명]                                  | 패키지 검색                           |                                 |
  | brew list                                              | 설치된 패키지 목록조회                 |                                 |
  | brew update                                            | Homebrew Update                      |                                 |

## Homebrew Cask 란?
  - masOS 앱, 폰트, 플로그인, 오픈소스가 아닌 소프트웨어를 설치할 수 있도록 해주는 Package
  
### How to Install ?
  ```console
  # homebrew 설치가 선행되어야 한다.
  $ brew install cask
  ```

### How to use ?
  ```console
  $ brew cask install Brackets
  ```









