# 목차 - Python

- 사용한 이유

- 한계와 대처방안

- 개념

  1. [tuple과 list의 차이점](#1-tuple과-list의-차이점)
	2. [파이썬의 메모리 관리](#2-파이썬의-메모리-관리)
  3. [Function call 혹은 A callable Object는 무엇인가](#3-function-call-혹은-a-callable-object는-무엇인가)
	4. [Passed by assignment](#4-passed-by-assignment)
  5. [Global Interpreter Lock](#5-global-interpreter-lock)
	6. [Iterators](#6-iterators)
  7. [Generator](#7-generator)
	8. [Closures](#8-closures)
  9. [Decorator](#9-decorator)
	10. [@classmethod와 @staticmethod](#10-classmethod와-staticmethod)
	11. [@property](#11-property)

# :white_check_mark: Python

## 사용한 이유

1. 사용자 친화적인 언어 > 인기가 많음
2. 풍부한 커뮤니티
3.  그로인해서 생긴 여러 라이브러리와 여러 사용처에서 쓰이는 범용성



## 한계와 대처방안

하드웨어에 직접 접근하고 아주 고성능의 속도를 내야 한다거나 혹은 모바일 프로그래밍을 해야한다면 파이썬은 적절하지 않다고 알고있다. 이에 맞는 언어나 플랫폼을 공부하여 적용하는 것이 바람직하다고 본다.



## 개념

### 1. tuple과 list의 차이점

- list는 mutable하고 tuple은 immutable하다. 
- 따라서 dict의 key로 올 수 있는건 tuple이다.
- iteration을 수행하는 속도는 tuple이 더 빠르다.
- 리스트의 경우 슬라이싱([:])이나 list()로 복사할 경우 새로운 메모리값이 할당되어 새로운 객체가 생성된다. 하지만 tuple은 원본 객체를 가리킨다.



### 2. 파이썬의 메모리 관리

> 일반적인 메모리 관리(stack, heap 영역)

개별적인 힙을 사용하여 메모리를 유지한다. 따라서 힙은 모든 파이썬 객체와 자료구조를 가지고 있다. 이 영역은 파이썬의 인터프리터만이 접근 가능하고 프로그래머는 사용할 수 없다. 또한 빌트인된 **가비지 컬렉터**를 통해서 비사용되는 메모리에 대해 힙공간을 관리한다.

**레퍼런스 카운트**를 이용해 참조되지 않은 객체는 메모리에서 해제된다.

레퍼런스 카운트를 이용해 메모리가 해제되지 않는 상황은 **순환참조, 서로 참조같은 상황**이다. 이때 가비지 컬렉터 모듈의 collect() 메소드는 순환참조 탐지 알고리즘을 통해 이와 같은 상황에도 메모리를 해제할 수 있다.



### 3. Function call 혹은 A callable Object는 무엇인가

파이썬에서 함수는 호출가능한 객체로 취급된다. 클래스 내부에 \__call__ 함수가 구현되어있다면 생성된 instance는 호출가능한 객체가 된다.



### 4. Passed by assignment

파이썬의 자료형에 따라 call by value 혹은 call by reference가 될 수 있다.

자료형이 immutable 타입의 객체를 넘기면 call by value가 되고, 그렇지않으면 call by reference가 된다.

immutable 자료형 : int, str, tuple 등

mutable 자료형 : list, dictionary 등



### 5. Global Interpreter Lock

> 스레드와 동시성

파이썬 인터프리터가 한 쓰레드만 하나의 바이트코드만 실행할 수 있게 하는 것.

GIL은 하나의 쓰레드에게 모든 자원의 점유를 허락한다.

파이썬은 모든 것이 객체이고 이 객체는 GC를 위해 레퍼런스 카운트를 가지고있다. 이 레퍼런스 카운트는 병렬 프로그래밍때 흔히 접할 수 있는 공유 자원 접근시 race condition 문제를 겪을 수 있게된다. 그렇게 되면 메모리 유실로 이어진다.

이때에도 mutex라는 방안이 있는데 파이썬 오브젝트 하나하나 마다 대응하는 mutex를 둔다면 여러개의 mutex는 성능적으로 많은 손해를 볼 뿐 아니라, deadlock이라는 치명적인 위험 상황을 불러들이게 되어 좋지 않은 결정이 된다.



### 6. Iterators

Iterable한 객체를 순회할 수 있게 하는 객체

리스트, Set, Dict와 같은 컬렉션이나 문자열과 같은 문자 시퀀스 등은 for 문을 써서 하나씩 데이터를 처리할 수 있다. 이들을 Iterable 객체라고 한다.

내장 함수 iter()는 그 Iterable 객체의 iterator를 리턴한다.

어떤 클래스를 Iterable 하게 하려면, 그 클래스의 iterator를 리턴하는 \__iter__() 메서드를 작성해야 한다.

실제 for 루프에서 iterable 객체의 \__iter__() 메서드를 호출하여 iterator를 가져온 후 next() 메서드를 통해 루프를 돌게 된다.



### 7. Generator

Iterator의 특수한 한 형태

yield 키워드를 사용하여 차례대로 반환한다.

데이터가 무제한이어서 모든 데이터를 리턴할 수 없는 경우나, 대량이어서 일부씩 처리하는 것이 필요한 경우 사용할 수 있다.



### 8. Closures

클로져는 함수 객체로서 다른 함수를 반환한다. 

일반 함수와 다르게 자신의 영역밖에서 호출된 함수의 변수값과 레퍼런스를 복사, 저장, 접근을 가능하게 한다.

기존에 만들어진 함수나 모듈 등을 수정하지 않고도 wrapper 함수를 이용해 커스터마이징을 할 수 있게해준다.



### 9. Decorator

일급객체와 클로져의 특성을 이용하여 함수 객체에서 새로운 기능을 추가하는 것을 쉽게 할수 있는 방법.

```python

# 데코레이터에 인자를 받고, 함수에도 인자를 받는 상황.
class DecoClass:
    """
    클래스로 데코레이터 만들기
    """

    def __init__(self, name=None):
        self.name = name

    def __call__(self, func, *args, **kwargs):
        print("1 데코 지정", self.name)
        return self.Wrap(func)

    class Wrap:
        def __init__(self, func):
            self.func = func

        def __call__(self, *args, **kwargs):
            print("2 데코 지정")
            return self.func(*args, **kwargs)


def deco_func(name):
    """
    함수로 데코레이터 만들기
    """

    def wrap(fn):
        print('deco', name)

        def wrap2(string, string2):
            return fn(string, string2)

        return wrap2

    return wrap


@deco_func('설기')
# @DecoClass('설기')
def common_func(string, string2):
    return "common_func : " + string + string2

  
print(common_func(string='hello', string2='good'))

```



### 10. @classmethod와 @staticmethod

이 두 데코레이터는 클래스안에서 사용된다.

일반적으로 클래스를 정의하고 instance를 생성한 후에 instance를 통해 클래스 메소드를 사용할 수 있지만, 두 데코레이터를 이용하면 클래스에서 직접적으로 호출할 수 있다.

가장 큰 차이점은 상속했을때 차이가 난다. `@classmethod` 는 cls 인자를 통해 현재 클래스의 객체에 접근할 수 있지만, `@staticmethod` 는 정의된 시점의 클래스 객체의 정보를 반환한다.

```python
class Dog:
    local = "usa"

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}의 나이는 {self.age}이고 {self.local}에 산다."

    @staticmethod
    def static_show(*args):
        return Dog(*args)

    @classmethod
    def class_show(cls, *args):
        return cls(*args)


class KoreaDog(Dog):
    local = "korea"


a = Dog("big", 10)
print(a)
# big의 나이는 10이고 usa에 산다.

b = KoreaDog.static_show('설기', 2)
print(b)
# 설기의 나이는 2이고 usa에 산다.

c = KoreaDog.class_show('설기', 2)
print(c)
# 설기의 나이는 2이고 korea에 산다.

```



### 11. @property

파이썬에서 클래스를 사용할때 private 한 속성을 최대한 가독성 쉽고 pythonic 하게 제어하는 방법은 `@property` 데코레이터를 사용하는 것이다.

```python
class Fridge:
    def __init__(self):
        self.celsius = 4

    @property
    def celsius(self):
        return self.__celsius

    @celsius.setter
    def celsius(self, temp):
        if temp < 0:
            self.__celsius = 0
        elif temp > 10:
            self.__celsius = 10
        else:
            self.__celsius = temp


f = Fridge()
print(f.celsius)

f.celsius = 11
print(f.celsius)

# 출처: https://velog.io/@kksh1205/python-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%EA%B3%BC-property-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0
```

