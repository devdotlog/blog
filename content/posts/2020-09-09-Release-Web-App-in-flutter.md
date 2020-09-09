---
title: "Flutter로 개발한 Web App을 Release 모드로 테스트하기"
date: 2020-09-09T08:42:00+09:00
draft: false
cagegory: flutter
tags: [flutter, web, release]
---

![release](https://sh0seo.github.io/images/release.png)

# Flutter로 개발한 Web App을 Release 모드로 테스트하기

Flutter를 이용하여 Web App을 개발했다면, 서버에 Deploy 하기 전에 Release 상태로 테스트를 진행해야 합니다.

테스트는 아래와 같은 순서로 진행합니다.

1. Web App을 Release 모드로 build
2. Web Server 실행
3. 테스트

## Release 모드로 build

대상 프로젝트로 이동하고 command 라인에서 다음 명령을 실행하여 빌드합니다.

```
> flutter build web
```

정상적으로 빌드가 되었다면 프로젝트 폴더 내에 ```프로젝트폴더/build/web``` 디렉토리에 index.html 파일을 포함한 release 파일들이 생성된 것을 확인할 수 있습니다.

![release-web](https://sh0seo.github.io/images/release-web.png)

## Web Server 실행

빌드된 소스는 정적 웹페이지 리소스입니다. apache, nginx 등의 웹 서버가 설치되어 있다면 로컬에서 바로 실행하여 테스트가 가능합니다. 
하지만 별도의 웹 서버 없이 go 혹은 python을 이용하여 손쉽게 이용이 가능합니다. 

### python 웹서버

미리 빌드한 build/web 디렉토리로 이동하여 아래와 같이 실행하면 ```127.0.0.1:8080``` 주소로 브라우저에서 확인이 가능합니다.

```
$ python -m http.server --bind 127.0.0.1 8080
```

### go를 이용한 웹서버

go는 파일 웹서를 위한 심플한 코딩이 필요합니다.

```
package main

import "net/http"

func main() {
  http.Handle("/", http.FileServer(http.Dir("./")))
  http.ListenAndServe(":8080", nil)
}
```

## Release 소스를 Deploy 없이 테스트

aws, google cloud, cafe24와 같은 웹 호스팅 계정이 있다면 테스트가 끝난 소스를 업로드하여 public 환경에서 테스트가 가능합니다. 하지만 그런 여건이 되지 않는다면 로컬 환경에서 구동된 상태로 외부에 공개할 수 있는 방법이 있습니다.

[ngrok](https://ngrok.com/) 서비스를 이용하면 로컬 서버를 public ip로 공개하여 누구나 사용할 수 있습니다. 무료만으로도 사용은 가능하지만 속도가 느리고 동시에 접속이 불가능한 점이 있지만, 급하게 시연을 하거나 개발 결과를 보고하기 위한 용도 정도를 사용이 가능합니다. ngrok은 go 언어를 이용하여 개발된 서비스로 windows, linux, macos 어떤 환경에서든 동일하게 사용이 가능합니다.

go(혹은 python)으로 구동 중인 로컬의 서버의 주소는 ```127.0.0.1:8080``` 라고 하면, ngrok 으로 아래와 같이 command에서 실행합니다.

```
$ngrok http 8080
```
ngrok이 실행되면 임시 URL이 화면에 출력됩니다. 해당 URL로 접속하면 로컬의 8080 서버로 http 요청을 전달해주게 됩니다.

![release-ngrok](https://sh0seo.github.io/images/release-ngrok-result.png)

## 참조

- [flutter의 공식 페이지 가이드](https://flutter.dev/docs/deployment/web#building-the-app-for-release)
- [ngrok 홈페이지](https://ngrok.com/)
