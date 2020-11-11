# 목차 - Django

- 사용한 이유

- 한계와 대처방안

- 개념

  1. [Django ORM의 작동 방식](#django-orm의-작동방식)

  2. [정적파일](#정적파일)


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