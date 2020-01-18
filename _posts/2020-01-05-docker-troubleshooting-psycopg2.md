---
title: django dockerizing 시 psycopg2 관련 설치 문제
categories: [TIL]
tags: [Docker, Django]
excerpt: "사소한 troubleshooting"
---
# 맥락

docker build 시에 `pip install -r requirements.txt` 에서 에러가 났음

deploy log를 살펴보니 psycopg2 설치 시에 에러가 발생했었음

# What I learned

`psycopg2` 디펜던시 문제로 파악됨

Dockerfile에 아래를 추가해주니 해결.

`RUN apk update && apk add postgresql-dev gcc python3-dev musl-dev`

# 출처

[https://github.com/psycopg/psycopg2/issues/684#issuecomment-392015532](https://github.com/psycopg/psycopg2/issues/684#issuecomment-392015532)