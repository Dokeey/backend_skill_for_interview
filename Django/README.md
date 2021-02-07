# 목차 - Django

- 사용한 이유

- 한계와 대처방안

- 개념

	1. [Django ORM의 작동 방식](#django-orm의-작동방식)
	2. [정적파일](#정적파일)
	3. [django-channels 라이브러리 기본적 이해(데이터 흐름)](#django-channels-라이브러리-기본적-이해데이터-흐름)


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

> 출처: https://github.com/KimSoungRyoul/Django_ORM_pratice_project/issues/7

- Lazy Loading 지연로딩
  - 정말 필요한 시점에만 호출된다.
  - 선언만 하면 SQL이 호출되지 않는다.
  - 정말 필요한 만큼만 호출한다.
    - ![image](https://user-images.githubusercontent.com/38549761/98473942-8f24d200-2238-11eb-86c0-f254e4d1dccc.png)
- Caching
  - 호출하는 순서가 바뀌는 것만으로도 캐싱때문에 발생하는 SQL 호출수가 달라질 수 있다.
    - ![image](https://user-images.githubusercontent.com/38549761/98474340-ac59a080-2238-11eb-8142-2b842b8f2b45.png)
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

### 정적파일

> 출처: https://blog.hannal.com/2015/04/start_with_django_webframework_06/

Static file과 Media file로 구분한다.

Static 파일은 Javascript, CSS, Image 파일처럼 웹 서비스에서 사용하려고 미리 준비해 놓은 정적 파일입니다. 파일 자체가 고정되어 있고, 서비스 중에도 수시로 추가되거나 변경되지 않고 고정되어 있습니다.

Media file은 이용자가 웹에서 올리는(upload) 파일입니다. 파일 자체는 고정되어 이지만, 언제 어떤 파일이 정적 파일로 제공되고 준비되는지 예측할 수 없습니다.

Django로 운영되는 프로젝트의 설정을 관리하는 `settings.py`에 Static file와 관련된 항목이 다섯 가지 존재하며, 보통은 다음 세 가지를 사용합니다.

- `STATICFILES_DIRS`

  - 개발 단계에서 사용하는 정적 파일이 위치한 경로들을 지정하는 설정 항목입니다.

  - ```python
    STATICFILES_DIRS = (
        os.path.join(BASE_DIR, 'static'),
    )
    ```

- `STATIC_URL`

  - 웹 페이지에서 사용할 정적 파일의 최상위 URL 경로입니다. 이 최상위 경로 자체는 실제 파일이나 디렉터리가 아니며, URL로만 존재하는 단위입니다. 

  - 문자열은 반드시 `/`로 끝나야 합니다. `findstatic` 명령어로 탐색되는 정적 파일 경로에 `STATIC_URL` 경로를 합치면 실제 웹에서 접근 가능한 URL이 됩니다.

  - `STATIC_URL`은 정적 파일이 실제 위치한 경로를 참조하며, 이 실제 경로는 `STATIC_ROOT` 설정 항목에 지정된 경로입니다.

  - ```python
    STATIC_URL = '/static/'
    ```

- `STATIC_ROOT`

  - `STATIC_ROOT`는 Django 프로젝트에서 사용하는 모든 정적 파일을 한 곳에 모아넣는 경로입니다. 한 곳에 모으는 기능은 `manage.py` 파일의 `collectstatic` 명령어로 수행합니다. 각 Django 앱 디렉터리에 있는 `static` 디렉터리와 `STATICFILES_DIRS`에 지정된 경로에 있는 모든 파일을 모읍니다.

  - `settings.py`의 `DEBUG`가 `True`로 설정되어 있으면 `STATIC_ROOT` 설정은 작용하지 않으며, `STATIC_ROOT`는 실 서비스 환경을 위한 설정 항목입니다. 

  - ```python
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
    ```

`settings.py`에 Media file와 관련된 항목이 두 가지 존재합니다.

- ` MEDIA_ROOT`는 이름이 `STATIC_ROOT`과 비슷한데, 업로드가 끝난 파일을 배치할 최상위 경로를 지정하는 설정 항목입니다. `MEDIA_ROOT`는 `STATIC_ROOT`와 다른 경로를 지정해야 합니다.

- `MEDIA_URL`은 `STATIC_URL`과 이름도 비슷하고 역할도 비슷합니다. `MEDIA_URL`도 `MEDIA_ROOT`와 마찬가지로 `STATIC_URL`과 URL 경로가 달라야 합니다.

- ```python
  MEDIA_URL = '/upload_files/'
  MEDIA_ROOT = os.path.join(BASE_DIR, 'uploads')
  ```

- ```python
  urlpatterns += static('/upload_files/', document_root=settings.MEDIA_ROOT)
  ```

  urls.py에 추가 설정이 필요하다.



### django-channels 라이브러리 기본적 이해(데이터 흐름)

django channels 라이브러리는 **장고의 소켓통신**을 위한 라이브러리 입니다.

장고의 기본적인 데이터흐름과 굉장히 비슷해 학습하는데 큰 어려움은 없습니다. 하지만 저처럼 웹소켓을 처음 다뤄보는 초보자의 경우 몇몇 부분은 이해하기 힘든 부분이 있었습니다.

그 중 consumer 부분은 가장 핵심적이면서도 이해하기 힘든 부분중 하나인데, 크게 4가지 메소드를 사용합니다.

1. connect()
2. receive()
3. disconnect()
4. group_send()와 type 메소드

이 4가지 메소드에 통해 자바스크립트와 장고 사이의 소켓통신 데이터 흐름을 다뤄보려 합니다.

#### connect()

connect() 메소드는 자바스크립트에서 **웹소켓 생성시** django에서 처음으로 응답받는 메소드입니다.

```javascript
const ws = new WebSocket(SOCKET_HOST + `/ws/users/`);
```

위 처럼 웹소켓 생성시 장고에서는 Routing과 middleware stack 들을 거치고 connect() 메소드에 도달합니다.

보통 connect() 메소드에 도달하고 인증과정을 거쳐 해당 유저를 적절한 Group에 속하게 하는 작업들을 합니다.

#### receive()

```javascript
ws.send(message);
```

receive() 메소드는 자바스크립트에서 **웹소켓을 통해 메세지 전송시** django에서 응답을 받는 메소드입니다.

주의할 점은 해당 메세지를 JavaScript 값이나 객체를 JSON 문자열로 변환해야 합니다. 그래서 위 코드보다는 

```javascript
ws.send(
      JSON.stringify({
        message,
      })
    );
```

이 같은 형태로 자주 사용했습니다.

```python
async def receive(self, text_data):
  text_data_json = json.loads(text_data)
  message = text_data_json['message']
```

receive() 메소드는 text_data 인자를 받아 위 처럼 데이터를 받아볼 수 있게 됩니다.

반대로 django에서 프론트쪽으로 메세지를 보내고 싶다면?

```python
# 현재 소켓으로 접속한 유저에게 전송
await self.send(text_data=json.dumps({
  'message': message,
}))

# 현재 소켓으로 접속한 유저와 그가 속한 그룹원들에게 전송
await self.channel_layer.group_send(
  self.room_group_name,
  {
    'type': 'chat_message',
    'message': message,
  }
)
```

위 처럼 send() 혹은 group_send() 메소드를 사용하면 됩니다.

다만 group_send()를 보낼땐 type 메소드를 지정해야하는데 뒤에서 알아보겠습니다.

#### disconnect()

```javascript
ws.close()
```

disconnect() 메소드는 위 처럼 자바스크립트의 **웹소켓의 종료 신호 전송시** 응답받는 메소드입니다.

반대로 django에서 프론트쪽으로 종료신호를 보내고 싶다면 `self.close()` 메소드를 사용하면 됩니다.

더 나아가 내가 속한 그룹원들도 전부 종료시키고 싶다면 아래와 같이 사용할 수 있습니다.

```python
await self.channel_layer.group_send(
            GROUP_NAME,
            {
                'type': 'websocket.close',
            }
        )
```

자바스크립트에서도 close 신호를 받았을때 어떻게 처리해줄지 커스텀을 해줄 수 있습니다.

```javascript
ws.onclose = function (e) {
	console.log(e)
  notification.open({
    message: "채팅이 종료되었습니다.",
    description: "새로운 채팅을 시작하세요",
    icon: <CloseCircleOutlined style={{ color: "red" }} />,
  });
};
```

#### group_send()와 type 메소드

만약 채널레이어 어느 한 그룹에 15명의 유저가 속해있다고 가정해봅시다. 저는 이 중에 12, 13번째 유저에게만 메세지를 보내고 싶습니다. 이런 상황에 `group_send()`와 type 메소드를 이용해 볼 수있습니다.

`group_send()`는` send()` 메소드와 다르게 현재 소켓통신으로 접속한 유저를 포함하여 모든 그룹원들에게 메세지를 전송할 수 있는 메소드입니다.

이렇게 보면 그렇게 헷갈려보이지 않은데 문제는 그 안에 반드시 지정해야하는 type 메소드 때문입니다.

type 메소드는 그룹원들 하나하나에 대응하여 메세지를 전송하는 역할의 메소드입니다. 글로는 어려우니 코드를 보겠습니다.

```python
async def receive(self, text_data):
  await self.channel_layer.group_send(
    self.room_group_name,
    {
      'type': 'user_message',
      'users': [12, 13]
    }
  )

async def user_message(self, event):
  users = event['users']
  message = ''
  if self.scope['user'].pk in users:
  	message = "hello!"

  await self.send(text_data=json.dumps({
    'message': message
  }))
```

위 코드에서 유심히 보셔야 할 곳은 `user_message()` 메소드의 `self.scope['user'].pk` 부분입니다. 지금까지의 해당 코드는 **현재 소켓통신으로 접속한 유저의 pk값** 이었습니다.

하지만 `user_message()` 안에서는 **그룹원들 개개인의 pk** 가 되어 각자가 차례차례 if 문을 거쳐 12, 13번째 유저를 찾아내 원하는 메세지를 보낼 수 있게됩니다.

#### 과정 요약

![image](https://user-images.githubusercontent.com/38549761/104471034-b4014100-55fd-11eb-95b2-d967a1a7abcf.png)