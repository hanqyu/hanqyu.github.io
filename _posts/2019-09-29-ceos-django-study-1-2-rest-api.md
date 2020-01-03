---
title: "API 서버 스터디  # 1-2 REST API에 대해"
categories: [Django]
tags: [스터디, Django, Django-rest-framework, 백엔드, API, Restful, TDD]
excerpt: 개인적으로 REST의 뜻을 해석하는 것보다 어떤 것이 RESTful하고 어떤 것이 RESTful하지 않은지를 보는게 이해가 더 빨랐다.
header:
 teaser: assets/images/teasers/django-808x367.png
---
⏪ 이전 포스트 확인하기: [API 서버 스터디  # 1-1 TDD란 무엇일까](/django/ceos-django-study-1-1-test-driven-development/) 

-----
> 💡 API 서버가 무엇이고 왜 필요한지 모르겠다면 [**이 글**](/blog/what-is-api-server/)을 참고해보자.  

우리가 주로 쓰게 될 **Django-rest-framework**을 배우기 전에 **REST API**가 무엇인지에 대한 이해가 먼저 필요하다. 

### REST API?

**REST API**에 대한 정확한 정의가 무엇이냐라고 물으면 항상 명쾌한 대답을 얻기가 어려웠던 것 같다. 
혹자는 REST API에 대한 개념을 정의하는게 필요치 않다고 얘기하기도 하고, 그렇게 모호하다보니 RESTful하냐 아니냐를 논의하는 것을 싫어하는 분도 보았다. 
그러다보니 [위키피디아 페이지](https://en.wikipedia.org/wiki/Representational_state_transfer)마저 논쟁이 잦고 다소 피상적인 설명으로 이루어져 있다고 한다.

그러므로 RESTful하냐 아니냐에 대해서 너무 신경 쓸 필요는 없다고 생각한다. 
이미 우리가 사용하는 대부분의 백엔드 API는 RESTful한 구조를 따르는 경우가 많고, 
대부분의 프레임워크 진영에서 REST API를 위한 라이브러리를 지원하므로 구현하는데 큰 이해가 필요하지도 않다. 
내가 이해하기엔 REST API라는 것이 어떤 아키텍쳐라기 보다는 컨벤션에 가깝게 느껴졌기 때문에 그런 정도로 받아들이면 될 것 같다.

> 📖 REST : Representational state transfer

개인적으로 REST의 뜻을 해석하는 것보다 어떤 것이 RESTful하고 어떤 것이 RESTful하지 않은지를 보는게 이해가 더 빨랐다. 아래 예시를 보자.

**Bad**

    GET /get_article?id=1
    POST /delete_article   id=1

**Good**

    GET /articles/1/
    DELETE /articles/1/

특징 중 하나는 첫째로는 URL에 CRUD 액션을 명시하지 않는다는 것이다. 
HTTP 메소드에 CRUD에 해당하는 **GET, POST, PUT, DELETE**가 있기 때문이다. 
아래 예시처럼 같은 `/articles/1/`에 HTTP 메소드만 바꿔주는 것만으로도 충분히 액션을 표현할 수 있다. 

그렇기 때문에 아래 예시는 RESTful하지 않다.

    POST /delete/article/1/
    POST /update/article/1/
    POST /create/article/
    POST /hide/article/1/
    POST /show/article/1/

둘째로는 자원의 접근 방법에 관한 것이다. 클라이언트 입장에서 서버에 있는 데이터를 자원이라고 한다. 
DB를 생각하면 쉽다. 테이블마다 레코드가 있고 각 레코드는 자원이다. 대부분의 API는 이 자원을 읽거나 변경하는 요청을 한다. 
그리고 특정 자원을 식별하기 위해선 id로서 그 자원을 명시해줘야 한다.

다시 예시를 보자.

**Bad**

    GET /get_article?id=1
    POST /delete_article   id=1

**Good**

    GET /articles/1/
    DELETE /articles/1/

query string으로서 `id=1` 값을 전달하는 것이 아니라 `/articles/1` 과 같이 직접적으로 자원에 접근하는 것과 같은 형태를 취하고 있다. 
조금 더 깔끔해지지 않았는가?

이 두가지 특징만 고려한다면 RESTful한 구조를 만들 수 있다. 
예를 들어 아래와 같은 API도 가능하다.

    GET /students/12/
    POST /students/
    
    GET /schools/10/students/
    PUT /students/12/grade/

이처럼 어떤 것이 RESTful하고 RESTful하지 않다고 하는지 예시만 기억해두면, REST API 구조를 만들 수 있다.

다음 글에서는 Django에서 쉽고 빠르게 REST API를 만들기 위한 framework. Django-rest-framework에 대해 다룰 것이다.
