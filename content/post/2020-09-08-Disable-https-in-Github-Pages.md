---
title: "Github Pages에서 HTTPS를 사용하지 않기"
date: 2020-09-25T00:00:00+09:00
draft: false
categories: github
tags: [github, pages]
---

![](https://sh0seo.github.io/images/https-main.png)

Github Pages는 정적 웹사이트를 제공하는 기능입니다. Github `계정명.github.io` 도메인을 자동으로 사용할 수 있는 괜찮은 서비스입니다. 기본적으로 [jekyll](https://jekyllrb-ko.github.io)을 이용하여 서비스를 제공합니다. 

`계정명.github.io` URL은 default로 https를 활성화 상태로 제공이 됩니다. `setting`으로 이동해서 보면 아래와 같이 Github Pages에 대한 설정을 변경할 수 있습니다.

![](https://sh0seo.github.io/images/https-setting.png)

# https를 사용하고 싶지 않다면?

만약, default로 제공되는 https를 사용하지 않는다면 어떻게 해야 할까요? default로 https를 제공하지만 `계정명.github.io` URL를 사용한다면 무조건 HTTPS 상태가 강제됩니다. 아래와 같이 `setting -> Github Pages -> Enforce HTTPS`는 설정을 변경할 수 없는 상태로 제공됩니다.

![](https://sh0seo.github.io/images/https-disable.png)

# 그럼 어떻게?

두가지 방법이 있습니다. 하나는 2016년 6월 15일 전에 개설한 github.io 계정을 찾는 방법이고 두 번째는 Custom Domain 사용하는 방법입니다. 사실상 Custom Domain을 사용하는 방법 밖에는 없는 상태입니다. 

Custom Domain이란 직접 구매한 도메인을 의미합니다. [가비아](https://www.gabia.com) 같은 곳에서 별도로 자신만의 도메인을 구압하여 Github Page 설정에서 CNAME에 저장하면 됩니다.

![](https://sh0seo.github.io/images/https-enable.png)

# 왜 공짜 https를 안쓰는 거지?

[jejucctv.site](http://jejucctv.site/)를 만들면서 http로 제공되는 HLS 스트리밍을 해야하는 작업이 있었습니다. 문제는 대부분의 웹브라우저는 보안 상의 문제로 혼합 콘텐츠(Mixed Content)를 허용하지 않습니다. 혼합 콘텐츠란 https 사이트 내에서는 http를 이용하여 외부 resource 사용 하지 못하는 것을 말합니다. 

이런 혼합 콘텐츠 문제로 https를 사용하지 않고 Github Page를 이용하기 위해서 http를 이용한 Github Page 사용 방법을 적용하였습니다.

# 참고

- [StackOverFlow의 'Is there a way to disable SSL/TLS for GitHub Pages?'](https://stackoverflow.com/questions/38910944/is-there-a-way-to-disable-ssl-tls-for-github-pages)
- [Mixed Content](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/fixing-mixed-content?hl=ko)