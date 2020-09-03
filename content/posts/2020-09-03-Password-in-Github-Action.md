---
title: "Github Action을 이용한 빌드 & 실행에서 패스워드 숨겨서 사용하기"
date: 2020-09-03T14:35:00+09:00
draft: true
categories: go
tags: [go, github, action]
---

# Github Actions?

![](https://sh0seo.github.io/images/github-actions.png)

Github에는 Actions라는 기능이 있습니다. Travis CI 처럼 소스코드를 이용해서 build, test, deploy 등의 기능을 수행할 수 있는 서비스입니다. 

Github Actions을 이용하면 hugo blog 빌드 & 배포, cron을 이용한 반복적인 업무 자동화도 할 수 있습니다.

## Github Actions에서 패스워드 숨기기

Github Actions을 이용해서 특정 DB에 데이터를 갱신하는 
