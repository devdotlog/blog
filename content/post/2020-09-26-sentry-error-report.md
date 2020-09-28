---
title: "Flutter Web 프로젝트에 Sentry 에러 리포팅 추가 및 분석"
date: 2020-09-26
cagegory: 
 - "flutter"
tags:
 - "flutter"
 - "sentry"
pager: true
draft: true
---

![](https://sh0seo.github.io/images/dart-main.png)

[dartpad.dev](https://dartpad.dev/)는 dart 언어를 직접 실행할 수 있는 REPL(read-eval-print loop) 사이트입니다. dart 개발 환경을 따로 구축하지 않고 dart 코드를 바로 실행해볼 수 있는 유용한 사이트입니다. 

## Flutter UI 테스트 기능

dartpad.dev에는 dart 코드 뿐만 아니라 Flutter 코드, 즉 UI를 테스트 할 수 있는 기능도 제공합니다. 에러가 없는 코드를 입력한 후에 `RUN`을 클릭하면 결과를 확인할 수 있습니다. 

![](https://sh0seo.github.io/images/dart-flutter-ui.png)

## 소스코드 공유 기능 부재

dartpad.dev에는 다른 REPL site와 달리 소스코드 공유 기능이 없습니다. 예를 들어 golang의 REPL 사이트인 [play.golang.org](https://play.golang.org/)에는 `share` 기능을 통해 테스트한 코드를 공유할 수 있습니다.

![](https://sh0seo.github.io/images/dart-golang.png)

## Gist를 이용한 Flutter UI 코드 공유하기

작성한 코드를 매번 따로 저장하는 일은 번거로운 일이 아닐수 없습니다. 
이런 단점을 보완할 수 있는 기능으로 [Gist](https://gist.github.com/)를 이용하는 방법이 있습니다.

1. gist에 public 상태로 에러 없는 Flutter 코드를 저장합니다.
2. 코드를 저장하면 아래와 같이 URL에 ID를 확인할 수 있습니다.

![](https://sh0seo.github.io/images/dart-gist.png)

3. gist의 id를 dartpad.dev에 URL Path로 지정합니다.

![](https://sh0seo.github.ioimages/dart-dartpad.png)

## 결론

Gist와 dartpad.dev를 이용하면 Flutter UI를 테스트하면서 코드를 공유도 가능합니다. 
