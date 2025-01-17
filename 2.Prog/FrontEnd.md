# FrontEnd
  - Browser
  - JavaScript
  - React
  - VueJS

---

# Browser
## Rendering
  - phase1. HTML을 파싱하여 DOM요소와 스타일을 포함하여 RenderTree을 계산
  - phase2. 계산된 RenderTree를 화면에 노출
    - phase2-1. Reflow: Render Tree에 대한 유효성 확인 작업과 Node의 크기/위치를 계산하는 작업
    - phase2-2. RePaint: Reflow가 발생하였을 때 화면에 다시 그리는 작업

### Reflow, Repaint 최소화
  - 동일한 행태의 소스코드를 Group화하여 작업
  - dispaly속성을 none으로 사용한 뒤, block으로 해제하고, 이 중간 코드에 style의 변경 코드를 넣어 대량으로 'reflow'가 발생하는 현상을 방지
  - 가상노드 사용
    - SPA(React, VueJS등)의 가상DOM처럼, 가상 node를 만들어 교체하는 작업으로 최소화
    - parentNode.replaceChild(cloneNode, originNode);


---

# Javascript
## Execution Context(실행 컨텍스트)
  - Javascript의 this, function, hoisting, closure, Scope 등의 동작원리를 담고 있는 핵심원리
  - 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념 -> 실행 가능한 코드가 실행되기 위해 필요한 환경(≒JRE)
    - 실행 가능한 코드란?
      - 전역코드: 전역제 존재하는 코드
      - Eval코드: eval 함수로 실행되는 코드
      - 함수코드: 함수 내에 존재하는 코드
      
  ![Execute Context](../resource/Prog,%20JavaScript,%20SPA,%20execution%20context.pdf)
      
## 2. prototype

## 3. function
### 1. literal(=코드상에 데이터를 표현하는 방식)
  - 익명함수
    ~~~
    // 함수를 정의하고 변수에 저장
    var v = function(x,y) { return x+y; };
    
    // 함수를 정의하고 바로 호출한다.
    var added = (function(x,y) {return x+y;})(1,2);
    ~~~
    
  - 함수선언식(Function Declaration)
    ~~~
    say();  //hoisting 가능
    function say(){
        console.log("hello world!");
    }
    ~~~
    
  - 함수표현식(Function Expression)
    ~~~
    fncSay();   //hoisting 불가능
    const fncSay = function(){
        console.log("hello world!");
    }
    ~~~

### Lexical Scoping(렉시컬 스코프)
  - 외부 -> 내부변수 접근 불가
  - 내부 -> 외부변수 접근 가능
    ~~~
    function outerFunction () {
      const outer = 'outer function!'
        
      function innerFunction() {
         const inner = 'inner function!'
         console.log(outer) // I’m the outer function!
      }
        
      console.log(inner) // Error, inner is not defined
    }
    ~~~

## Regular Expression
  - 정규표현식 
    ~~~
    var regExNUM         = /[0-9]/;                                                 // 숫자
    var regExNUM_8       = /^[0-9]{8}$/;                                            // 숫자8자
    var regExNUM_13      = /^[0-9]{13}$/;                                           // 숫자13자
    var regExNotNUM      = /[^0-9]/;                                                // !숫자
    var regExKOR         = /[가-힣ㄱ-ㅎㅏ-ㅣ]/g;                                        // 한글 정규식
    var regExENG         = /[a-zA-Z]/;                                              // 영문(대·소문자)
    var regExSYM         = /([\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"])/;     // 특수문자
    var regEx_whiteSpace = /\s/;                                                    // 공백
    var regEx_password   = /^(?=.*[a-zA-Z])(?=.*[^a-zA-Z0-9])(?=.*[0-9]).{8,16}$/;  // 비밀번호 검사(영문,숫자,특수문자를 포함한 8~16자)

    var regExCnsc_3rdKOR   = /([가-힣ㄱ-ㅎㅏ-ㅣ\x20])\1\1/;                                // 같은 한글 연속 3번 정규식
    var regExCnsc_3rdENG   = /(\w)\1\1/;                                               // 같은 영문자&숫자 연속 3번 정규식
    var regExCnsc_3rdSYM   = /([\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"])\1\1/;  // 같은 특수문자 연속 3번 정규식
    var regExAdjcnt_3rdNum = /(012)|(123)|(234)|(345)|(456)|(567)|(678)|(789)/;        // 연속된 숫자 정규식

    var regExHp         = /^\d{3,4}-\d{3,4}-\d{3,4}$/;                                 // 휴대전화번호 정규식(hypen)
    var regNomalExHp    = /^\d{9,12}$/;                                                // 휴대전화번호 정규식
    var regBirthDt      = /^\d{4}-\d{2}-\d{2}$/;                                       // 생년월일 정규식(hypen)
    var regNomalBirthDt = /^\d{8}$/;                                                   // 생년월일 정규식
    var regExEmail      = /^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z]?)*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z]?)*\.[a-zA-Z]{2,3}$/i;	//Email 정규식
    ~~~

---


# WebPack
  - webpack setting
  - web.config.js
      - root directory에 'webpack.config.js' file 생성
      - 주요 항목
          - context: root directory 를 지정한다.
              - entry는 해당 path를 시작으로 진행되나, output은 해당 path를 시작으로 빌드 되지 않는다.(path.resolve(__dirname, 'need path')를 이용) 왜지 ?!
              - ex>
                  path.resolve(__dirname, 'src/main/frontend'),
          - resolve: contrext의 대상선정?!
              - ex>
                  resolve: {
                    extendsion: ['.js', '.jsx']
                  }
          - entry: root file로써, entry를 시작으로 필요한 모듈들을 다 불러온 후, 한 파일로 합쳐 bundle.js에 저장.
                   추가적으로는 모듈을 통하여 ES6 문법으로 작성된 코드를 ES5 형태로 변환.
              - ex>
                  entry: {
                    main: './src/App.js'
                  }
          - output: 산출물
              - ex>
                  const _publicPath = '/public/dist';
                  ...
                  output: {
                    path: path.resolve(__dirname, 'src/main/resources/public/dist'),
                    publicPath: _publicPath,
                    filename: 'app.bundle.js'
                  }
          - module
            - rules: JavaScript File들을 어떻게 처리할 것인지 정하는 config
          - plugins
          
      ```console
      Source 경로
        - module.exports = {context: path.resolve(__dirname, 'Source 경로') ... }
      Build SRC
        - module.exports = { ... entry: {Build SRC entry: '경로'} ... }
      Build DST
        - module.exports = { ... output: {path: __dirname, filename: '경로'} ... }
      ```
        
      ```console
      var path = require('path');
      
      module.exports = {
          context: path.resolve(__dirname, 'src/main/frontend'),
          entry: {
              main: './MainPage.jsx'
          },
          output: {
              path: __dirname,
              filename: './src/main/webapp/js/react/[name].bundle.js'
          },
          module: {
              rules: [ {
                  test: /\.jsx?$/,
                  exclude: /(node_modules)/,
                  use: {
                      loader: 'babel-loader',
                      options: {
                          presets: [ '@babel/preset-env', '@babel/preset-react' ]
                      }
                  }
              }, {
                  test: /\.css$/,
                  use: [ 'style-loader', 'css-loader' ]
              } ]
          },
          //plugins: [],
          mode: 'none',
          devtool: 'sourcemaps',
          cache: true,
      };
      ```
    
  - webpack plug-in
    - clean-webpack-plugin: output으로 지정한 디렉토리를 build할 때마다 삭제하여 주는 plug-in
      ~~~
      npm install --save-dev clean-webpack-plugin
      ~~~
    - html-webpack-plugin: 번들링 시, 기본 HTML문서의 template을 생성해주는 plug-in
      ~~~
      npm install --save-dev html-webpack-plugin
      ~~~
    - html-webpack-root-plugin: html-webpack-plugin을 확장한 plugin으로써, html-webpack-plugin이 생성하는 HTML문서에 elements를 추가할 수 있다.
      ~~~
      npm install --save-dev html-webpack-root-plugin
      ~~~
    - uglifyjs-webpack-plugin: js파일을 난독화 및 압축하여 주는 plug-in
      ~~~
      npm install --save-dev uglifyjs-webpack-plugin
      ~~~
    - helmet: meta information 설정
      ~~~
      npm install --save react-helmet @types/react-helmet
      ~~~
      
      
  - 참고사이트: [https://webpack.js.org/concepts/](https://webpack.js.org/concepts/)
  - 참고사이트: [https://github.com/webpack/docs/wiki/configuration](https://github.com/webpack/docs/wiki/configuration)

---


# React
## React interlocking in Spring Boot
  - React 앱을 생성
     ~~~
     npm install -i react react-dom
     
     또는
     
     npm install -g create-react-app
     ~~~
     
  - 의존성 추가(Webpack, Babel, Loader)
     ~~~
     npm i webpack webpack-cli @babel/core @babel/preset-env @babel/preset-react babel-loader css-loader style-loader -D
     ~~~
     - babel-loader : 자바스크립트 모듈 번들링을 위한 로더이며, 보통 ES6 코드를 ES5로 변환하기 위해 사용한다.
     - css-loader : 모듈 번들링은 자바스크립트 기반으로 이뤄지기 때문에 CSS 파일을 자바스크립트로 변환하기 위해 사용한다.
     - style-loader : css-loader에 의해 모듈화 되고, 모듈화 된 스타일 코드를 HTML 문서의 STYLE 태그 안에 넣어주기 위해 사용된다.
     - url-loader : 스타일에 설정된 이미지나 글꼴 파일을 문자열 형태의 데이터(Base64)로 변환하여 해당 CSS 파일 안에 포함시켜버리기 때문에 정적 파일을 관리하기 쉬워진다. 하지만 실제 파일들보다 용량이 커지고, CSS 파일이 무거워지므로 적당히 사용하는 것을 권장한다.
     - file-loader : 정적 파일을 로드하는데 사용되며, 보통 프로젝트 환경에 맞게 특정 디렉토리에 파일을 복사하는 역할을 수행한다.
     
  - 의존성 추가(PropTypes, React Router)
     ~~~
     npm install --save prop-types react-router-dom
     ~~~
       - prop-types: React의 Prop를 validation할 때 사용.(React v15.5 부터 다른 패키지로 이동(즉, React.PropTypes사용 불가)하였으며, 현재는 prop-types lib를 사용하는 것을 권고.)
       - react-router-dom: react-router를 의존하고 있기 때문에, 해당 component만 설치한다.

## UI
### Meterial-UI
    - Install
      ~~~
      npm install --save @material-ui/core
      ~~~
      
## Transfer
### Axios
    - Install
      ~~~
      npm install --save axios
      ~~~

---


# VueJS
  - 참고사이트: [https://cli.vuejs.org/guide/webpack.html](https://cli.vuejs.org/guide/webpack.html)
  - 참고사이트: [https://cli.vuejs.org/config/](https://cli.vuejs.org/config/)
