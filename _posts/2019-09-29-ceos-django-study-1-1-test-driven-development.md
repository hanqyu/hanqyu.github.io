---
title: "API 서버 스터디  # 1-1 TDD란 무엇일까"
layout: single
classes: wide
author_profile: true
read_time: true
comments: true
share: true
related: true
sidebar:
 nav: "django-study"
categories: [Django]
tags: [스터디, Django, Django-rest-framework, 백엔드, API, Restful, TDD]
excerpt: Test Driven Development. 테스트 주도 개발이란 무엇일까.
header:
 teaser: assets/images/teasers/django-808x367.png
---

⏪ 이전 포스트: [API 서버 스터디 # 0 개요](/django/ceos-django-study-0-overview/) 
⏩ 다음 포스트: [API 서버 스터디 # 1-1 TDD란 무엇일까](/django/ceos-django-study-1-1-test-driven-development/) 

-----

## #1 - TDD와 DRF

### TDD(Test Driven Development)란 무엇일까

> 💡 [개발인턴경험기 - 1. TDD의 중요성은 알겠는데, 어떻게 하는거지?](/blog/intern-1-what-is-tdd)를 참조하시면 현업에서 TDD를 쓰는 사례와 Ruby On Rails에서의 간단한 테스트 코드를 확인하실 수 있습니다.

**TDD(Test Driven Development, 테스트 주도 개발)**는 대학생이 경험하기 참 어려운 개발 프로세스 중 하나이다. 그도 그럴 것이 장기적인 유지보수를 생각하지 않는 대부분의 대학 프로젝트를 고려하면 당연히 테스트를 고려할 필요가 없기도 하다.

하지만 많은 개발자가 협업을 하고, 소프트웨어가 장기적으로 유지보수 될 필요가 있는 환경에서는 테스팅은 꼭 필요하다. 그리고 그런 중요한 테스팅을 강조하는 개발 프로세스가 TDD인 것이다.

> 📖 **테스트 주도 개발**  
> 테스트 주도 개발은 매우 짧은 개발 사이클을 반복하는 소프트웨어 개발 프로세스 중 하나이다. 개발자는 먼저 요구사항을 검증하는 자동화된 테스트 케이스를 작성한다. 그런 후에, 그 테스트 케이스를 통과하기 위한 최소한의 코드를 생성한다.  
> (위키백과)

여기서 염두에 두어야 할 점은 테스트하는 것 자체가 TDD인 것은 아니다. 그냥 테스트 코드를 작성하는 것과 TDD가 다른 점은, TDD는 프로그램의 코드를 작성하기 전에 테스트 코드를 먼저 작성한다는 것이다. 그래서 테스트 **주도** 개발인 것이다.

### 예시

개발자는 프로그램에 기능을 더하기 이전에 테스트 코드를 먼저 작성한다. 

자신이 만드려는 기능의 목적을 알고 있기 때문에 테스트 코드가 어떤 식으로 작성되어야 할지는 전혀 어렵지 않다. 
예를 들어 `text` 필드에 어떤 String를 담아 보내면 `Blog`라는 테이블에 저장하는 API를 만들고 싶다고 가정해보자.   

프로그램이 어떻게 돌아가는지는 아직 모르지만 일단 이 API "잘 돌아간다"고 얘기할 수 있는 요건은 최소한 두 가지가 있다.
정상적인 요청을 보냈을 때 아래 요건이 만족되어야 한다.
1. 응답으로 status code가 200이 반환될 것.
2. 요청한 String이 잘 저장될 것. 

위 내용을 담아 일단 테스트 코드를 작성해본다.
> Django-rest-framework를 사용했을 때의 예시라 DRF가 익숙하지 않은 분들은 잘 읽히지 않을 수도 있다. 하지만 그냥 프로세스 자체만 알아두시면 된다.

```python
"""
테스트 코드
"""

class APITest(APITestCase):
    def test_success(self):
        # POST 요청
        response = self.client.post(path='/api/test/', data={'text': 'test입니다'})
          
        # status code 200일 경우 성공
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        
        # text가 성공적으로 저장되었을 경우 성공
        self.assertEqual(Blog.objects.filter(text='test입니다').exists(), True)  
```

그리고 나서 테스트를 실행해보자. 

```
Failure
Traceback (most recent call last):
    ... 
    self.assertEqual(response.status_code, status.HTTP_200_OK)  # status code 200일 경우 성공
AssertionError: 404 != 200
```
당연히 오류가 날 것이다. api가 만들어져 있지 않으니 `/api/test/`에 요청을 보낼 수 없어서 그렇다.

그러면 이제 `/api/test/`에 해당하는 api를 만든다. 먼저 `urls.py`에 routing을 하고 다시 테스트를 돌린다.
```python
urlpatterns= [ 
    ...
    path('api/test/', TestAPI.as_view()),
]
```

```
Error
Traceback (most recent call last):
...
ImportError: cannot import name 'TestAPI' from 'core.views'

Process finished with exit code 1
```

TestAPI를 만든 적이 없어서 에러가 날 수밖에 없을 것이다. 그러면 이제 `views.py`에 `TestAPI`를 작성한다.

```python
from rest_framework import status
from rest_framework.response import Response
from rest_framework.views import APIView


class TestAPI(APIView):
    def post(self, request, *args, **kwargs):
        return Response(status=status.HTTP_200_OK)
```

```
Failure
Traceback (most recent call last):
  ...
    self.assertEqual(Blog.objects.filter(text='test입니다').exists(), True)  # text가 성공적으로 저장되었을 경우 성공
AssertionError: False != True
```

다시 테스트를 돌려보면, 이제는 `self.assertEqual(Blog.objects.filter(text='test입니다').exists(), True)`에 해당하는 코드를 충족시키지 않아서 테스트가 실패한다. 즉, 해당 라인까지 에러 없이 넘어가고 있는 것으로 보아 api가 요청을 잘 받고는 있다는 뜻이다(Status Code: 200).

그러면 이제 TestAPI에 Blog를 저장하는 코드를 추가한다.
 
```python
from core.models import Blog

class TestAPI(APIView):
    def post(self, request, *args, **kwargs):
        text = requests.data.get('text')
        Blog.objects.create(text=text)
        
        return Response(status=status.HTTP_200_OK)
```

이제 테스트가 통과될 것이다.
```
Tests passed: 1 of 1 test - 12 ms
```


### 테스트 "주도" 개발

위 예시에서 확인했듯, TDD가 일반적으로 생각하는 개발 프로세스와 다른 점이 있다.
1. 테스트 코드를 먼저 작성한다.
2. 테스트를 짧은 주기로 실행해보면서, 점진적으로 코드를 작성해 나간다.

위 두 가지 특징이 테스트 **주도** 개발의 가장 큰 특징이다. 위 예시와 같이 아주 간단한 코드를 작성하는 일이라면 오히려 이 방법이 불편하게 느껴질 수도 있다.
하지만 프로그램 간의 의존성이 복잡하거나, 프로그램 자체의 복잡성이 클 경우 이 방법이 매우 효율적인 방법이 될 수 있다. 쉽게 말해 작성하기 어려운 프로그램일 수록 TDD가 효과적이라는 것이다.

### 디버깅도 쉽다

TDD의 위력은 디버깅에서 발휘된다.  

![IDE에서 디버깅하기]({{"assets/images/content/2019-09-29/debug.png" | absolute_url}})
프로그램이 복잡할 때면 디버깅하기가 쉽지 않다. 특히 프론트에서 렌더링할 수 있는 경우가 아니라 API 서버를 만들고 있다면 매번 요청을 보내면서 print문으로 line by line으로 테스팅을 하는 짓은 개발 시간을 배는 늘리는 짓이다.

디버깅 기능을 잘 제공해주는 JetBrain 계얼의 IDE의 경우 테스트 코드마다 바로 바로 디버그 툴을 띄울 수 있도록 GUI에서 지원해주기도 하니 적극 이용해보자. 

----
- 알아볼 수 있도록 예쁜 코드 작성하기
- 커밋 메세지 규칙화하기
- 깃헙 Readme 정리하기

와 같이 중요하면서도 단번에 습득하기 어려운 일들이 있다. TDD도 이들과 같이 습관을 들여야 하는 중요한 일 중 하나라고 생각한다.

그리고 대학생 입장에서 이들의 공통점은 **이걸 하는게 더 낭비 같은데?** 라는 생각이 든다는 것이다. 협업 환경에 놓여있지 않으니, 이 프로세스가 쌓였을 때의 위력을 실감하기 쉽지 않다.

귀찮더라도 차근차근히 습관을 들이는 것이 최고다. 급할수록 돌아가자. 

