---
title: cannot import six from django.utils
categories: [TIL]
tags: [Django, dependencies]
excerpt: "장고 3.0 의존성 문제 해결하기"
---
# 맥락

django-celery-beat를 설치 후 실행했을 때

`cannot import six from django.utils`

에러가 django-timezone-field에서 나는 경우가 있음.

# What I learned

django-timezone-field를 4.0 버전으로 업해주면 됨

requirements.txt 를 직접 4.0으로 수정하거나

`pip install 'django-timezone-field==4.0'`

django-timezone-file는 django 3.0에 대응한 버전 4.0을 내놓았지만 다른 디펜던시들이 아직 그 버전을 따라가지 못하는 것임.

버전업을 강제로 한 이상 어떤 에러가 날지는 모릅니다.

일부 디펜던시 pip check에서 에러가 날 수 있는데, pip check error를 무시해도 되는 경우에 사용하는 임시방편이다. 

# Django 3.0 관련 Issues

**Github Issues**에서 django 3.0 대응 상황을 확인할 수 있으니 참고해보자

**django-rest-framework**

*: DRF는 별다른 이슈가 없다*

- [Fix Django 3.0 deprecations #7074](https://github.com/encode/django-rest-framework/pull/7074) `Merged`
- [dj 3.0 #7070](https://github.com/encode/django-rest-framework/pull/7070) `Merged`

**django-celery-beat**

- [django-timezone-field breaking celery-beat for Django 3 #304](https://github.com/celery/django-celery-beat/issues/304)
- [django.utils.six has been removed in django3 #289](https://github.com/celery/django-celery-beat/issues/289)

**django-grappelli**

- [Django 3.0 #927](https://github.com/sehmaschine/django-grappelli/issues/927)

**django-admin-tools**

- [Add support for django 3.0 #102](https://github.com/django-admin-tools/django-admin-tools/pull/102)

**django-allauth**

- [fix: Django 3.0 Deprecation warning for is_safe_url #2442](https://github.com/pennersr/django-allauth/pull/2442)
- [Test Django 3.0 & Python 3.8 on Travis #2441](https://github.com/pennersr/django-allauth/pull/2441)