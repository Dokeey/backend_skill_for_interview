# 목차 - Django

- 사용한 이유

- 한계와 대처방안

- 개념

  1. [Django ORM의 작동 방식](#django-orm의-작동방식)

  2. [정적파일 서빙](#정적파일-서빙)

  3. [세션 관리](#세션-관리)


# :white_check_mark: Django

## 사용한 이유

1. Python 기반 웹 프레임워크이기 때문에 파이썬에서 가능한 모든 동작이 가능하며, 강력한 라이브러리가 많다.
2. 이미 구현되어 있는 기능이 많고 다양한 라이브러리로 빠르게 개발이 가능하다.



## 한계와 대처방안

1. 서버 사이드 렌더링
   - 장고로 풀 스택을 고집하지 않고 프론트엔드를 부분적으로 클라이언트 사이드 렌더링 기술을 사용한다.(JavaScript, 리액트, 뷰 등)
2. 기본적으로 비동기 관련 도구를 제공하지 않는다.
   - 이미 훌륭한 라이브러리가 존재하며, 장고 3.0부터는 비동기에 대한 지원을 시작하고 있다.



## 개념

### Django ORM의 작동 방식

- Lazy Loading 지연로딩
  - 정말 필요한 시점에만 호출된다.
  - 선언만 하면 SQL이 호출되지 않는다.
  - 정말 필요한 만큼만 호출한다.
    - ![image-20201109025254023](/Users/hood/Library/Application Support/typora-user-images/image-20201109025254023.png)
- Caching
  - 호출하는 순서가 바뀌는 것만으로도 캐싱때문에 발생하는 SQL 호출수가 달라질 수 있다.
    - ![image-20201109025333545](/Users/hood/Library/Application Support/typora-user-images/image-20201109025333545.png)
- Eager Loading
  - for문을 통해 쿼리셋을 호출할 시 N+1번 SQL을 호출할 수 있다.
  - select_related()와 prefetch_related()라는 메서드로 해당 문제를 해결
- 쿼리셋은 1개의 메인쿼리와 0~N개의 추가 쿼리로 이루어져 있다.
- select_realated
  - 정방향 참조 모델에 사용하며 join을 통해 1개의 쿼리로 만들어 준다.
- prefetch_realated
  - 역방향 참조 모델에 사용하며 추가 쿼리를 만들어 준다.
- 실수하기 쉬운 것
  - prefetch_realated는 filter 뒤에 사용하자.
  - ORM을 아예 포기하지말고 .raw()를 사용하자
  - 역방향참조모델을 filter()절에 넣어서 JOIN을 유도하자

### 정적파일 서빙

### 세션 관리

