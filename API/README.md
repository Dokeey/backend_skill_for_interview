# :white_check_mark: API

## 목차


- [:white_check_mark: ​인증](#인증)
- [OAuth](#oauth)
    
- [기본적인 인증](#기본적인-인증)
    
- [토큰 인증](#토큰-인증)
    
- [JWT](#jwt)
- [REST](#rest)
- [REST API](#rest-api)
- [RESTful](#restful)
- [Django Rest Framework](#django-rest-framework)



## :white_check_mark: ​인증

### OAuth

> 출처: https://showerbugs.github.io/2017-11-16/OAuth-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C

- OAuth란
  - 외부 서비스에서도 인증을 가능하게 하고 그 서비스의 API를 이용하게 해주는 것, 이것을 바로 OAuth라고 한다.
- OAuth 인증프로세스
  - ![image](https://user-images.githubusercontent.com/38549761/99749657-49aeb180-2b22-11eb-8bb5-b988bcf9360c.png)

### 기본적인 인증

일반적인 웹 인증에는 HTTP 프로토콜을 이용하여 ID와 Password를 통해 인증한다. 하지만 HTTP는 stateless 성질이 있기 때문에 응답이 완료되면 인증된 정보를 저장하고 있지 않는다.

그래서 쿠키와 세션을 이용하여 인증한다. 

쿠키는 클라이언트에 저장되는 텍스트 형식의 데이터이다. 쿠키에는 사용자 인증이 유효한 시간을 명시할 수 있으며, 한 번 유효 시간이 정해지면 브라우저를 끄더라도 인증이 유지된다. 예를 들어 '아이디와 비밀번호를 저장하시겠습니까?' 문구와 쇼핑몰의 장바구니 기능이 있겠다.

세션은 쿠키를 기반하였고 사용자 정보 파일을 서버측에서 관리한다. 세션 ID를 부여하며 웹브라우저가 서버에 접속해서 브라우저를 종료할 때까지 인증상태를 유지한다. 그러므로 사용자가 많아질수록 서버 메모리를 많이 차지하여 성능저하의 요인이 될 수 있다. 그래서 요즘은 이 문제를 보완한 토큰 기반의 인증방식을 사용한다.



### 토큰 인증

토큰 인증방식은 세션 방식의 문제점을 보완하기 위해 나온 방식이다.

토큰 기반 시스템은 stateless 하다.

로그인된 클라이언트에게 토큰을 발급하고 클라이언트는 토큰을 저장해두었다가 요청시마다 토큰과 함께 전송한다. 서버는 토큰을 검증되면 해당 응답을 보낸다.

쿠키보다 보안성은 좋지만 보안적으로 취약점은 존재할 수 있다.

### JWT

웹 토큰 인증 방식에 쓰이는 가장 대표적인 방식이다.

- 구조

  - ![image](https://user-images.githubusercontent.com/38549761/99751325-57b20180-2b25-11eb-925f-78017b34a208.png)

  - 헤더

    - 헤더에는 두가지 정보를 지니고 있다.

    - typ: 토큰의 타입

    - alg: 해싱 알고리즘을 지정. 토큰을 검증할 떄 사용되는 signature 부분에서 사용됨

    - ```
      {
        "typ": "JWT",
        "alg": "HS256"
      }
      ```

  - 페이로드

    - 서버에서 보낼 데이터가 들어감. 일반적으로 사용자의 ID, 유효기간이 포함
    - 클라이언트에 대한 정보, 메타 데이터같은 내용이 들어있고, base64로 인코딩 되어있음
    - Payload 에 담는 정보의 한 ‘조각’ 을 클레임이라고 부르고, 이는 name / value 의 한 쌍으로 이뤄져있습니다. 토큰에는 여러개의 클레임 들을 넣을 수 있다.
    - 클레임의 정보는 등록된 (registered) 클레임, 공개 (public) 클레임, 비공개 (private) 클레임으로 세 종류가 있습니다.

  - 서명

    - 헤더, 페이로드 그리고 secret key를 더한 후 서명됨



- 보안성

  - 헤더와 페이로드는 base64로 인코딩만 되므로 누구나 디코딩하여 확인할 수 있다.

  - 따라서 페이로드에는 중요한 정보가 포함되면 안된다. 하지만 signature는 secret key를 알지 못하면 복호화할 수 없다.

  - 해커가 JWT 토큰을 탈취하여 페이로드를 변경한다고 해도 signature에서 유효하지 않은 토큰으로 간주하게 된다.

- 단점

  - 한 번 발급되면 유효기간이 완료될 때 까지는 계속 사용이 가능하므로 유효기간이 지나기 전까지 정보 탈취 가능
  - 유효기간을 짧게 하면 재로그인이 잦아지고 길면 해커에게 탈취 가능성이 큼



## REST

> Roy Fielding의 문서
>
> 출처: https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html

- 정의

  - Representational State Transfer의 약자이고 자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다.
  - 월드 와이드 웹(www)과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 개발 아키텍처의 한 형식
    - REST는 기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 **웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.**

- 개념

  - HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.
  - 즉, REST는 자원 기반의 구조(ROA, Resource Oriented Architecture) 설계의 중심에 Resource가 있고 HTTP Method를 통해 Resource를 처리하도록 설계된 아키텍쳐를 의미한다.

- 장점

  - HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
  - REST API 메시지가 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
  - 여러가지 서비스 디자인에서 생길 수 있는 문제를 최소화한다.
  - 서버와 클라이언트의 역할을 명확하게 분리한다.

- 단점

  - 사용할 수 있는 메소드가 4가지 밖에 없다.
    - HTTP Method 형태가 제한적이다.
  - 구형 브라우저가 아직 제대로 지원해주지 못하는 부분이 존재한다.
    - PUT, DELETE를 사용하지 못하는 점

- 필요한 이유

  - ‘애플리케이션 분리 및 통합’
  - ‘다양한 클라이언트의 등장’
  - 최근의 서버 프로그램은 다양한 브라우저와 안드로이폰, 아이폰과 같은 모바일 디바이스에서도 통신을 할 수 있어야 한다.

- REST 구성요소

  - 자원(Resource): URI
    - 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
    - 자원을 구별하는 ID는 ‘/groups/:group_id’와 같은 HTTP URI 다.
    - Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.
  - 행위(Verb): HTTP Method
    - HTTP 프로토콜의 Method를 사용한다.
    - HTTP 프로토콜은 GET, POST, PUT, DELETE 와 같은 메서드를 제공한다.
  - 표현(Representation of Resource)
    - Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답(Representation)을 보낸다.
    - REST에서 하나의 자원은 JSON, XML, TEXT, RSS 등 여러 형태의 Representation으로 나타내어 질 수 있다.
    - JSON 혹은 XML를 통해 데이터를 주고 받는 것이 일반적이다.

- REST 특징

  1. **Server-Client(서버-클라이언트 구조)**
- 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
    - 서로 간 의존성이 줄어든다.
    
  2. **Stateless(무상태)**
  - HTTP 프로토콜은 Stateless Protocol이므로 REST 역시 무상태성을 갖는다.
  
  - Client의 context를 Server에 저장하지 않는다.
    - 즉, 세션과 쿠키와 같은 context 정보를 신경쓰지 않아도 되므로 구현이 단순해진다.
  
- Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
      - 각 API 서버는 Client의 요청만을 단순 처리한다.
      - Server의 처리 방식에 일관성을 부여하고 부담이 줄어들며, 서비스의 자유도가 높아진다.
  
  3. **Cacheable(캐시 처리 가능)**
  - 웹 표준 HTTP 프로토콜을 그대로 사용하므로 웹에서 사용하는 기존의 인프라를 그대로 활용할 수 있다.
      - 즉, HTTP가 가진 가장 강력한 특징 중 하나인 캐싱 기능을 적용할 수 있다.
      - HTTP 프로토콜 표준에서 사용하는 Last-Modified 태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.
    - 캐시 사용을 통해 응답시간이 빨라지고 REST Server 트랜잭션이 발생하지 않기 때문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상시킬 수 있다.
  
  4. **Layered System(계층화)**
- Client는 REST API Server만 호출한다.
    - REST Server는 다중 계층으로 구성될 수 있다.
      - API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상의 유연성을 줄 수 있다.
      - 또한 로드밸런싱, 공유 캐시 등을 통해 확장성과 보안성을 향상시킬 수 있다.
    - PROXY, 게이트웨이 같은 네트워크 기반의 중간 매체를 사용할 수 있다.
    
5. **Code-On-Demand(optional)**
  - Server로부터 스크립트를 받아서 Client에서 실행한다.
  - 반드시 충족할 필요는 없다.
    
  6. **Uniform Interface(인터페이스 일관성)**
  - URI로 지정한 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
    - HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
      - 특정 언어나 기술에 종속되지 않는다.

## REST API

- REST API란
  - API(Application Programming Interface)란
    - 데이터와 기능의 집합을 제공하여 컴퓨터 프로그램간 상호작용을 촉진하며, 서로 정보를 교환가능 하도록 하는 것
  - REST API의 정의
    - REST 기반으로 서비스 API를 구현한 것
    - 최근 OpenAPI(누구나 사용할 수 있도록 공개된 API: 구글 맵, 공공 데이터 등), 마이크로 서비스(하나의 큰 애플리케이션을 여러 개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능하도록 만든 아키텍처) 등을 제공하는 업체 대부분은 REST API를 제공한다.

- REST API의 특징

  사내 시스템들도 REST 기반으로 시스템을 분산해 확장성과 재사용성을 높여 유지보수 및 운용을 편리하게 할 수 있다.

  REST는 HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현할 수 있다.

## RESTful

- RESTful이란

  RESTful은 일반적으로 REST라는 아키텍처를 구현하는 웹 서비스를 나타내기 위해 사용되는 용어이다.

  ‘REST API’를 제공하는 웹 서비스를 ‘RESTful’하다고 할 수 있다.

  RESTful은 REST를 REST답게 쓰기 위한 방법으로, 누군가가 공식적으로 발표한 것이 아니다.

  즉, REST 원리를 따르는 시스템은 RESTful이란 용어로 지칭된다.

- RESTful의 목적

  이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것

  RESTful한 API를 구현하는 근본적인 목적이 성능 향상에 있는 것이 아니라 일관적인 컨벤션을 통한 API의 이해도 및 호환성을 높이는 것이 주 동기이니, 성능이 중요한 상황에서는 굳이 RESTful한 API를 구현할 필요는 없다.

- RESTful 하지 못한 경우

  Ex1) CRUD 기능을 모두 POST로만 처리하는 API
  Ex2) route에 resource, id 외의 정보가 들어가는 경우(/students/updateName)



## Django Rest Framework

장고에서 REST API를 편하게 구현, 사용하기 위한 프레임워크