---
title: 에러) No 'Access-Control-Allow-Origin' header is present on the requested resource
categories: [TIL]
tags: [Django, CORS]
excerpt: APPEND_SLASH=True 설정과 CORS 에러
---
# 맥락

django-cors-headers가 모두 적용되어 있었음

`"No 'Access-Control-Allow-Origin' header is present on the requested resource"`

![CORS 에러 메세지]({{"assets/images/content/2020-02-13/cors.png" | absolute_url}})

그럼에도 불구하고 CORS 에러가 남

# What I learned

`APPEND_SLASH = True`

로 되어 있으면 요청한 URL 맨 뒤에 `/` 슬래시가 없는 경우에 `django.middleware.common.CommonMiddleware`에서 리다이렉션을 시킴. 

그래서 `CorsMiddleware`가 `CommonMiddleware` 보다 먼저 작동하도록 해야함

    MIDDLEWARE = [
    		...
        'django.middleware.csrf.CsrfViewMiddleware',
        'corsheaders.middleware.CorsMiddleware',
    		...
    ]

을 아래처럼 바꿔야 함

    MIDDLEWARE = [
    		...
        'corsheaders.middleware.CorsMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
    		...
    ]

[https://docs.djangoproject.com/en/2.2/_modules/django/middleware/common/](https://docs.djangoproject.com/en/2.2/_modules/django/middleware/common/)

> If APPEND_SLASH is set and the initial URL doesn't end with a
slash, and it is not found in urlpatterns, form a new URL by
appending a slash at the end. If this new URL is found in
urlpatterns, return an HTTP redirect to this new URL; otherwise
process the initial URL as usual.

Django 도큐먼트의 CommonMiddleware 설명을 보면 return HTTP redirect를 한다고 함. 그렇기 때문 CORS 정책에 걸릴 수 있는 것임.

# 출처

["No 'Access-Control-Allow-Origin' header is present on the requested resource" in django](https://stackoverflow.com/a/35987979)