# Kotlin

---


# Kotlin
  - Dmitry Jemerov. and Svetlana Isakova., 2017, 『Kotlin In Action』, 오현석 옮김, AcornPub

## 1장. 코틀린이란 무엇이며, 왜 필요한가?
  - Kotlin이란, Java PlatForm에서 돌아가는 새로운 프로그래밍 언어.
  - 간결하고 실용적이며, 자바 코드와의 상호운용성을 중시한다.
  - 대표적으로 서버개발, 안드로이드 앱 개발 등의 분야에서 사용할 수 있다.

### 코틀린 맛보기
  - 예제 실행 사이트: [http://try.kotl.in](http://try.kotl.in)
  ```
  data class Person(val name: String,                      // 데이터 클래스
                    val age: Int? = null)                  // 널이 될 수 있는 타입(Int?)과 파라미터 디폴트 값
  fun main(args: Array<String>){                           // 최상위 함수
      val persons = listOf(Person("영희"),
                           Person("철수", age=29))          // 이름을 붙인 파라미터
      val oldest = persons.maxBy{ it.age ?: 0 }            // 람다 식과 엘비스 연산자
      println("나이가 가장 많은사람: $oldest)                   // 문자열 템플릿
  }
  
  //결과: 나이가 가장 많은 사람: Person(name=철수, age=20)        // toString 자동 생성
  ```
  
### 코틀린의 주요 특성
  - 대상 플랫폼
    - 서버, 안드로이드 등 자바가 실행되는 모든 곳
    - iOS 디바이스: 인텔의 멀티OS 엔진을 사용하여 실행가능.
    - DeskTop 디바이스: 토네이도FX, 자바FX 등을 함께 사용하여 실행가능.
    - Front-End: 2017년03월01일 부로 자바스크립트로도 코틀린을 컴파일 가능.
  - 정적 타입 지정언어(Kotlin, Java 등)
    - 모든 프로그램 구성 요소의 타입을 컴파일 시점에 알 수 있고
    - 프로그램 안에서 객체의 필드나 메소드를 사용할 때마다 컴파일러가 타입을 검증해준다는 뜻.
    - 다만, Kotlin과 Java의 차이는 존재한데, 대부분의 경우 코틀린 컴파일러는 문맥으로부터 변수 타입을 유추하여 자동으로 타입을 정의해준다.
    ```
    var x = 1    // 코틀린은 변수를 정의하는 곳의 리터러를 통해, 변수 x를 정수형으로 초기화한다.
    ```
  - 동적 타입 지정언어(Groovy, JRuby 등)
    - 타입과 관계없이 모든 값을 변수에 넣을 수 있고
    - 필드나 메소드 접근에 대한 검증이 실행 시점에 일어나며, 그에 따라 코드가 더 짧아지고 데이터 구조를 더 유연하게 사용 가능
    - 다만, 실행시점에 오류를 알 수 있다는 단점이 존재.
  - 함수형 프로그래밍과 객체지향 프로그래밍(여기서 함수형 프로그래밍은 람다식을 포함한다)
    - 함수형 프로그래밍을 통해 소스가 간결해진다.
    - Thread Safety하다.
  - 무료 오픈소스

### 코틀린 응용
  - 코틀린 서버 프로그래밍
  - 코틀린 안드로이드 프로그래밍

### 코틀린 철학
  - 실용성
  - 간결성
  - 안전성
  - 상호운용성

### 코틀린 도구 사용
  * 난, Jetbrains Toolbox를 구매하여 사용 중 임으로 별도 글은 작성하지 않음.

## 2장. 코틀린 기초
  - 뒷절에 상세히 설명이 나오고, 이미 OOP를 공부한 사람이라면 큰 영양가?!가 없는 내용임으로 기재하지 않음
  
## 3장. 함수 정의와 호출
  - Java는 다양한 정적 메소드들을 모아두는 역할만 담당할뿐, 클래스와 인스턴스로의 의미가 전혀 없는 클래스들이 존재한다.
  - 이를 Kotlin에서는 의미 없는 클래스는 생략되도록 사용한다.
  - Java는 다양한 편의를 제공하기 위해, 많은 수의 생성자를 오버로딩하여 제공할 수 있다.
  - 이를 Kotlin에서는 디폴트값을 이용하여, 소스를 더욱 간결하게 표혀한다.
  
  ```
  // Java
  package strings
  
  public class JoinKt{
      public static String joinToString(...){...}
  
      // Java의 오버라이딩 영역
      public static String joinToString(Collection collection){...}
      public static String joinToString(Collection collection, String separator){...}
      public static String joinToString(Collection collection, String separator, String prefix){...}
      public static String joinToString(Collection collection, String separator, String prefix, String postfix){...}
  }
  
  // Kotlin
  // Kotlin 파일(확장자포함)을 Join.kt로 기재하였을 경우 위 코드와 완전히 동일하다.
  // 단, Kotlin 파일(확장자포함)명과 상이한 클래스 명으로 사용하고 싶을경우 "JvmName" Annotation을 package 구문 바로 앞에 사용한다.
  
  @file: JvmName("StringFunctions")
  package strings
  
  fun joinToString(...): String {...}
  
  // Kotlin에서 생성자 오버라이딩을 금지하기 위한 영역
  fun <T> joinToString(
      collection: Collection<T>,
      separator: String = ", ",
      prefix: String = "",
      postfix: String = ""
  ): String
  ```

## 4장. 클래스, 객체, 인터페이스
### 4.1.3. 가시성 변경자: 기본적으로 공개(=접근제어자)
  
## Annotation
  - @file: JvmName
  ```
  // Java
  package strings
  
  public class JoinKt{
      public static String joinToString(...){...}
  }
  
  // Kotlin
  // Kotlin 파일(확장자포함)을 Join.kt로 기재하였을 경우 위 코드와 완전히 동일하다.
  // 단, Kotlin 파일(확장자포함)명과 상이한 클래스 명으로 사용하고 싶을경우 "JvmName" Annotation을 package 구문 바로 앞에 사용한다.
  
  @file: JvmName("StringFunctions")
  package strings
  
  fun joinToString(...): String {...}
  ```
  
  - @JvmOverloads
  - Java에서는 디폴트 파라미터 값이라는 개념이 없어서 Kotlin 함수를 Java에서 호출하는 경우에는 그 Kotlin 함수가 디폴트 파라미터 값을 제공하더라도 모든 인자를 명시해야 한다.
  - Java가 Kotlin 함수를 자주 호출해야 한다면 Java쪽에서 좀 더 편하게 Kotlin 함수를 호출하고 싶을 것이다.
  - 그럴 때 @JvmOverloads Annotation을 함수에 추가할 수 있다.
  - 해당 Annotation을 추가하면, Kotlin Compiler가 자동으로 맨 마지막 파라미터로부터 파라미터를 하나씩 생략한 오버로딩한 Java 메소드를 추가해준다.
  ```
  // Kotlin
  @JvmOverloads
  fun<T> joinToString (
      collection: Collection<T>,
      separator: String = ", ",
      prefix: String = "",
      postfix: String = ""
  ): String {...}

  // Java
  String joinToString(Collection<T> collection, String separator, String prefix, String postfix){...}
  String joinToString(Collection<T> collection, String separator, String prefix){...}
  String joinToString(Collection<T> collection, String separator){...}
  String joinToString(Collection<T> collection){...}
  ```
