---
title: 파이썬 postgresql 안전한 방법으로 접속하기 
categories: [Django]
tags: [database, python]
excerpt: instance 사용 시 context manager를 강제 해보았습니다.
---
# 맥락

지금 개발하고 있는 서비스는 기존의 다른 서버가 있고 회사 사정으로 불가피하게 새로 서버를 올리고 있는 단계였습니다. 그렇다고 해서 기존의 서버를 닫은 상태는 아니고 특정 기능에서는 여전히 쓰이고 있었습니다. 

- 그래서 장고에서 기본으로 연결하고 있는 DB가 아니라 다른 디비에 연결할 필요가 있음.
- 하지만 곧 기존의 서버를 모두 옮겨올 것이기 때문에 모델을 짜서 강하게 결합할 필요까지는 없음.

이 상황은 MSA를 적용하려는 서비스에도 해당 될 것으로 생각됩니다.

아무튼 그런 상황에서 기존의 디비를 안전하게 연결하려면 connection과 cursor를 꼭 종료해주어야 할 것 같았는데, `close()`를 호출하지 않았다고 해서 connection이 계속 열려있는 상황이 생기기를 원치는 않았습니다.

*(개인적으로 실수를 하지 않는 것이 개발자의 덕목이 아니라 실수를 방지할 수 있는 체계를 만드는 것이 중요하기 때문에..)*

그래서 생각난 것이 python의 context manager를 이용해서 db를 사용할 수 있도록 해야겠다는 생각이 들었습니다.

context manager는 흔히 머신러닝 쪽 코드에서 많이 보이는데,

    with Foo() as foo:
    	foo.bar()

이렇게 생긴 코드가 context manager를 사용하고 있다고 보면 됩니다.

context manager의 장점은 with문을 벗어날 때 instance를 안전하게 삭제하는데, 삭제할 때 db의 connection을 끊어주면 안전하게 연결을 종료할 수 있습니다.

# What I learned

아래 출처를 참고해서 만든 DB 연결 코드는 아래와 같음.

    import contextlib
    import logging
    
    import psycopg2
    from django.conf import settings
    
    from shared.second_db.settings import DBNAME, USER, PASSWORD, HOST, PORT
    
    logger = logging.getLogger(__name__)
    
    
    class ClientException(Exception):
        pass
    
    
    class Client:
        def __init__(self):
            self.conn = psycopg2.connect(dbname=DBNAME, user=USER, host=HOST, password=PASSWORD, port=PORT)
            self.cur = self.conn.cursor()
    
        def __new__(cls, *args, **kwargs):
            client = super().__new__(cls)
            client.__init__(*args, **kwargs)
            return contextlib.closing(client)
    
        def close(self):
            self.cur.close()
            self.conn.close()
    
        def execute(self, query):
            self.cur.execute(query)
            return self.cur.fetchall()
    
        def ok(self):
            try:
                self.execute('SELECT 1')
                return True
            except (psycopg2.OperationalError, psycopg2.InterfaceError):
                return False
    
    
    # Example
    with Client() as client:
        print(client.ok())
    
    # Wrong Example
    client = Client()
    client.ok()

아래 Wrong Example처럼 `client = Client()`로 instantiate해서 사용하게 되면 아래와 같은 에러를 뱉을 것입니다.

    Traceback (most recent call last):
      File "<input>", line 1, in <module>
    AttributeError: 'closing' object has no attribute 'ok'

`contextlib.closing(client)` 이 코드 중 closing을 파헤쳐보면 그 이유를 알 수 있습니다.

    class closing(AbstractContextManager):
        def __init__(self, thing):
            self.thing = thing
        def __enter__(self):
            return self.thing
        def __exit__(self, *exc_info):
            self.thing.close()

우리가 `Client.__new__()`에서 리턴하고 있는 것은 Client의 instance가 아니라 closing의 instance를 뱉고 있었고, 
Client의 instance에 접근하기 위해서는 with context manager를 사용해야만 `__enter__(self)`를 통해 thing이라는 instance에 접근할 수 있는 것입니다.


# 출처

[How do I correctly clean up a Python object?](https://stackoverflow.com/a/30349291/11321149)