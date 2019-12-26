---
title: "#0 - Django로 2달 만에 실전 API 서버 만들기"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories: [Django]
tags: [스터디, Django, Django-rest-framework, AWS-EC2, AWS-RDS, Nginx, uWSGI, Fabric, AWS-CloudFront, 백엔드, API, Restful, ubuntu]
excerpt: 실제 프로덕션 레벨의 API 서버 만들기 0편 
header:
 teaser: assets/images/teasers/django-808x367.png
---

### #0 - 스터디 개요

### 스터디의 목적

신촌 연합 IT 창업 동아리 CEOS에서는 매 학기마다 팀을 모아서 IT 창업에 도전합니다. 동아리 내 개발자들이 원하는 서비스를 개발할 수 있도록 프론트엔드/백엔드 스터디를 진행했고, 이번 백엔드 스터디는 Django를 기반으로 진행되었습니다.

>목표는 최대한 빠르게 API 서버를 만드는 것이었고, `개념보다는 실전`이라는 생각으로 진행하였습니다.

스터디원은 컴퓨터공학과에 재학 중인 학생으로 이루어져 있고, 저 포함 5명이었습니다. 제가 스터디 진행을 맡았습니다.

파이썬에 대한 기본 지식은 모두 어느 정도 있었고, 백엔드를 띄워본 경험이 있는 스터디원은 4명 중 1명이었습니다. 대부분 API 서버에 대한 개념이 없었기에 이 글을 보시는 초보분들도 쉽게 따라 하실 수 있을 것 같습니다.

이 포스팅은 프로덕션 서버가 띄워지기까지의 과정을 다루고 자세한 코드 예제를 다루지는 않습니다.

**따라서 이런 분들께 추천합니다.**
1. API 서버에 대한 개념을 잡고자 하시는 분
2. 처음 API 서버를 띄워보려는데 어떻게 시작할지 막막한 분
3. 백엔드 스터디를 진행해보려는데 레퍼런스가 필요하신 분

**이런 분들은 추천하지 않습니다.**
1. DB를 만져본 적이 없다.
2. 모델링을 해본 적이 없거나 무엇인지 모른다.
3. 파이썬을 전혀 할 줄 모른다.
4. 실제 프로덕션이 아니라 연습용으로 Django를 배우시려는 분

>만약 파이썬에 익숙치 않다면 간단하게 튜토리얼을 통해 배우시는 걸 추천합니다.

_개인적으론 [Codacademy](https://www.codecademy.com/catalog/language/python)가 가장 빠르게 익히는데 도움이 되었던 것 같습니다. 다만, Class나 객체지향에 대한 개념이 부족하기 때문에 위키독스의 [5. 파이썬 날개달기](https://wikidocs.net/28) 파트를 읽어보시는 걸 추천합니다._
  

  
  
  
### 스터디 일정

스터디는 주 1회 4시간씩 진행되었고, 거의 매주 과제가 있었습니다. 창업 동아리다 보니 각자 만들어야 하는 서비스가 있었기 때문에 그 서비스를 만들면서 동시에 진행되었습니다.

- **1주차: Django 튜토리얼**
    - Django 공식 튜토리얼 따라해보기
    - MVC 패턴이란 무엇인가

- **2주차: TDD와 DRF**
    - TDD(Test-driven development)는 무엇이고 왜 해야 하는가
    - Rest API란 무엇인가
    - DRF(Django-rest-framework)는 무엇인가  

- **3주차: Git과 GitHub, 그리고 AWS EC2**
    - Git 기초
    - GitHub에 프로젝트 올리기
    - EC2 우분투에서 간단한 환경 설정하기
    - remote 서버에서 GitHub 프로젝트 clone하기
    
- **4주차: AWS RDS와 Django 설치 및 셋팅**
    - RDS 인스턴스 띄우고 database 생성하기
    - EC2 우분투에 Django 설치
    - 우분투에서 추가적인 설정하기
    
- **5주차: production 배포하기**
    - Nginx
    - uWSGI

- **6주차: 배포 자동화**
    - Fabric
    
- **7주차: 도메인 호스팅, HTTPS, AWS S3**
    - 도메인 구입하기
    - 도메인 AWS Route 53에 연결하기
    - HTTPS 셋팅 방법 두 가지
    - AWS CloudFront를 이용해 HTTPS 셋팅하기
    - Django에 AWS S3 연결하기
    

**Django로 2달 만에 실전 API 서버 만들기**, 다음 장(Django 튜토리얼)부터 시작합니다. 