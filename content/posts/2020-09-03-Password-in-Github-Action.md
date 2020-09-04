---
title: "Github Action을 이용한 빌드 & 실행에서 패스워드 숨기기"
date: 2020-09-03T14:35:00+09:00
draft: true
categories: go
tags: [go, github, action]
---

# Github Actions?

![](https://sh0seo.github.io/images/github-actions.png)

Github에는 Actions라는 기능이 있습니다. Travis CI 처럼 소스코드를 이용해서 build, test, deploy 등의 기능을 수행할 수 있는 서비스입니다. 

Github Actions을 이용하면 hugo blog 빌드 & 배포, cron을 이용한 반복적인 업무 자동화도 할 수 있습니다.

## Github Actions을 이용해서 DB 데이터 업데이트하기

만약에 일주일에 한번씩 DB를 업데이트 해야하는 일이 있다고 가정합니다. 

1. 사용하는 DB용 클라이언트를 실행
2. login/password로 로그인
3. 업데이트 query를 수행하기 위한 데이터 확인
4. Query를 실행
5. 잊지 않고 정해진 요일에 반복 수행

아마도 개발자는 위와 같은 단계를 반복적으로 수행하게 됩니다. 
