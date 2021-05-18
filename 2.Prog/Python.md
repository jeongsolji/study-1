# Python
  - Python
  - DJango
  - Flask


---
# Python[Python]([https://docs.python.org/ko/3/](https://docs.python.org/ko/3/)]

## Python 이란?

## Grammar
### 자료형
#### 기본자료형
    - 숫자
        - 정수[ int ]
        - 실수[ float ]
        - 지수, 8진수, 16진수 등
    - 문자열
    - boolean

#### 자료구조
    - 변수
    - 리스트
    - 튜플
    - 딕셔너리
    - 집합

---
# DJango

## DJango 란?

## ORM(Object Relational Mapping)

|  Seq  -|  Relation                                       |  Question  |  QuestionAndChoice  |  Choice  |  Describe                                          |
|--------|-------------------------------------------------|------------|---------------------|----------|----------------------------------------------------|
|  No.1  |  One To One                                     |      1     |          -          |     1    |  Question과 Choice가 반드시 하나씩 매핑 되어야 할 때  |
|  No.2  |  One To Many(ForeignKey)                        |      1     |          -          |     N    |  Question하나에 Choice가 여러개 매핑 되어야 할 때     |
|  No.3  |  Many To Many[중개모델(Intermediary Model)필요]  |      N     |        1 / 1        |     N    |  QuestionAndChoice를 하나 두고 QuestionAndChoice에서 Question과 Choice를 각각 Many To One으로 연결  |

* JOIN 방법
    - No.1
        -첫번째: Question을 기준으로 Choice
            > Question.objects.select_related('choice')
    - No.2(3가지 방법으로 조인 가능)
        -첫번째: Choice를 기준으로 Question(Many To One)
            > Choice.objects.select_related('question')
        -두번째: Question을 기준으로 Choice(One To One)
            > Question.objects.prefetch_related('choice')
            > Question.objects.annotate(choice_id=F('choice__id'), choice_text=F('choice__choice_text'), votes=F('choice__votes')).annotate(Count('id))

* 쿼리표현식[Query Expressions]
    - create
    - update
    - filter
    - order by
    - annotate: .annotate()는 필드 하나를 만들고 거기에 '어떤 내용' 을 채우게 만드는 것
        -참고: [https://velog.io/@magnoliarfsit/ReDjango-8.-QuerySet-Method-2](https://velog.io/@magnoliarfsit/ReDjango-8.-QuerySet-Method-2)
    - aggregate: .aggregate()는 QuerySet에 대해 계산된 집계를 dict로 반환
        -참고: [https://velog.io/@magnoliarfsit/ReDjango-8.-QuerySet-Method-2](https://velog.io/@magnoliarfsit/ReDjango-8.-QuerySet-Method-2)

* Q 객체 / F 객체
    - Q 객체: 복잡한 데이터베이스 쿼리를 작성할 때 사용
        -참고: https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.Q
    - F 객체: F() 객체는 모델의 필드 혹은 어노테이트된 열의 값을 나타낸다. 실제로 데이터베이스에서 Python 메모리로 가져오지 않고, 모델 필드 값을 참조하고 이를 데이터베이스에서 사용하여 작업
        -경쟁조건[race condition] 문제를 해결할 수 있는 방법이다.(이건 Thread Safety하게 한다는거 같아.)
        ```
        # 일반적인 Code
        question = Question.objects.get(question_text='hello')                // Python으로 quiestion.quiestion_text 값을 가져온다.(이때 가져오는지, 밑에 코드로 가져오는지.. lazy_loading에 따라 좀 다를수 있다. 단, DB에서 Python으로 데이터를 가지고 온 뒤 연산을 한다는데 의미가 있다.)
        question.stories_filed += "word"                                // Python에서 문자열 'word'를 더한다.
        question.save()                                                // DB에 저장한다.


        # F 객체를 사용했을 때 Code
        from django.db.models import F

        Question = Question.objects.get(question_text='hello')                // Python으로 quiestion.quiestion_text 값을 가져온다.(이때 가져오는지, 밑에 코드로 가져오는지.. lazy_loading에 따라 좀 다를수 있다. 단, DB에서 문자열을 더하라는 Query로 변경 후에 DB에서 처리하도록 유도한다는데 의미가 있다.)
        question.stories_filed = F('stories_filed') + 1                        // F 객체를 이용해서 DB에서 문자열 연산을 하도록 DJango 내부적으로 Query를 만든다.
        question.save()                                                // 위의 F객체를 이용해서 만든 Query를 실행시켜 저장토록 한다.
        ```

    - 참고: https://docs.djangoproject.com/en/2.0/ref/models/expressions/#f-expressions
    - 참고: https://blog.myungseokang.dev/posts/django-f-class/

