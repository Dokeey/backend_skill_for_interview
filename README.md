# 면접을 위한 백엔드 기술정리

> 했봤던건 최대한 깊게, 안해봤던건 개념정도만 숙지
>
> 출처: https://github.com/devJang/developer-roadmap

### 중요도

:white_check_mark: : 필수로 숙지

:ballot_box_with_check: : 천천히 숙지해도 좋음

### 학습 범위 

- 주로 사용하는 프로그래밍 언어

- 위의 언어와 함께 쓰이는 프레임워크

- 나의 이력서/포트폴리오에 쓴 기술들 

- 그외 도움될법한 기술들 

### 학습 우선순위 

1. 주 사용 언어 

2. 주 사용 프레임워크 

3. 지원한 팀에서도 사용하고, 나의 이력서/포트폴리오에도 쓴 기술

4. 지원한 팀에서는 사용하지 않지만, 나의 이력서/포트폴리오에 쓴 기술

5. 지원한 팀에서는 사용하지만, 나의 이력서/포트폴리오에 없는 기술

### 상세 정리 내용

- 굳이 이 기술을 사용한 이유

- 한계와 대처방안

- 개념



## :white_check_mark: 현재 포트폴리오 기술

[Python](Python/README.md)

[Django](Django/README.md)

PostgreSQL

Docker

AWS

NginX

Travis CI

React



## [:white_check_mark: Python](Python/README.md)

- 사용한 이유

- 한계와 대처방안

- 개념

1. tuple과 list의 차이점

2. 파이썬의 메모리 관리

3. Function call 혹은 A callable Object는 무엇인가

4. Passed by assignment

5. Global Interpreter Lock

6. Iterators

7. Generator

8. Closures

9. Decorator

   

## [:white_check_mark: Django](Django/README.md)

- 사용한 이유
- 한계와 대처방안
- 개념

1. Django ORM의 작동 방식
2. 정적파일 서빙



## :white_check_mark: [API](API/README.md)

- :white_check_mark: ​인증

    - OAuth

    - 기본적인 인증

    - 토큰 인증

    - JWT
- REST
- REST API
- RESTful
- Django Rest Framework



## :white_check_mark: 관계형 DB

### PostgreSQL



## :white_check_mark: ​DB 상세 정보

### ORM

### ACID

### 트랜잭션

### N+1 문제

### 정규화

### 인덱스와 작동 방식



## :white_check_mark: 테스팅

### 통합 테스트

### 단위 테스트

### 기능 테스트



## 컨테이너화 vs 가상화

### :white_check_mark: ​Docker



## :white_check_mark: ​CI/CD

### Travis CI



## :ballot_box_with_check: 웹 소켓

## :ballot_box_with_check: 웹 서버

### :white_check_mark: ​Nginx



## [:white_check_mark: 개발 일반지식](devel_common_sense/README.md)

### OOP

### Call by value vs Call by reference



## :white_check_mark: 웹 개발 일반지식

### 인터넷은 어떻게 작동될까

### HTTP는 무엇인가

### 브라우저와 동작 원리

### DNS와 작동원리

### 도메인 네임은 무엇인가

### 호스팅은 무엇인가



## [:white_check_mark: ​OS 및 일반지식](OS_common_sense/README.md)

### OS의 일반적 작동 방식

### 프로세스 관리

### 스레드와 동시성

### 데드락(deadlock)

### 메모리 관리

### 프로세스간 통신 (IPC)

### 입출력(I/O) 관리

### POSIX 기초

### 기본적인 네트워크 개념



## :white_check_mark: 캐싱

### CDN

### 서버 사이드

#### Redis



### 클라이언트 사이드



## :white_check_mark: 웹 보안

### MD5와 사용하지 않는 이유

### SHA Family

### scrypt

### bcrypt

### 해시 알고리즘

### HTTPS

### 컨텐츠 보안 정책

### CORS

### SSL/TLS

### OWASP 보안 정책



## :white_check_mark: ​개발과 설계 원칙

### SOLID

### KISS

### YAGNI

### DRY



## :white_check_mark: ​아키텍처 패턴

### 모놀로틱 앱

### 마이크로 서비스

### SOA

### :ballot_box_with_check: CQRS와 이벤트 소싱

### Serverless



## 검색 엔진

### :white_check_mark: ​Elasticsearch



## :ballot_box_with_check: 메세지 브로커

### :white_check_mark: ​RabbitMQ



## :ballot_box_with_check: GraphQL

### :white_check_mark: ​Apollo



## :white_check_mark: ​확장성

> 프로덕트 지속 가능성에 대해 배우며 관심을 기울여야 하는 일반적인 주제

### 마이그레이션 전략

#### Graceful

#### Degradation

#### Throttling

#### Backpressure

#### Loadshifting

#### Circuit Breaker



### 수평적 확장 vs 수직적 확장

### 관찰 가능성을 고려한 확장

### 측정 vs 모니터링 vs 원격측정