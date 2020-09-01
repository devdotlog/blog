---
title: "Text normalization in Go"
date: 2020-09-01T04:15:00+09:00
draft: false
tags: go, unicode, normalization, 유니코드, 정규화
---

# 나만 몰랐던 유니코드 정규화

아래와 같이 특수 문자가 섞여 있는 문자열이 있습니다. 

```"São Paulo, Brazil. Wien, Österreich."```

이런 문자열을 아래와 같이 알파벳으로 변경하고 싶다면 어떻게 해야 할까요?

```"Sao Paulo, Brazil. Wien, Osterreich."``` 

고민 없이 쉽게 할 수 있는 방법은 대상 문자를 찾아서 원하는 문자로 치환하는 방법입니다.
치환해야 할 문자 종류가 많지 않다면, 간단하게 적용할 수 있는 방법입니다.

```
package main

import (
    "fmt"
    "strings"
    )

func main() {
    data := "São Paulo, Brazil. Wien, Österreich."
    fmt.Printf("Before: %s\n", data)
    
    after := strings.Map(normalize, data)
    fmt.Printf("After: %s\n", after)
}

func normalize(in rune) rune {
    switch in {
    case 'ã':
        return 'a'
    case 'Ö':
        return 'O'
    }
    return in
}
```
[https://play.golang.org/p/PmFFH5frx0H](https://play.golang.org/p/PmFFH5frx0H)

그런데 치환 대상 문자의 개수가 **1750개 이상**이라면 어떻게 해야 할까요?

이럴 때 필요한 것이 **Unicode Normalization(유니코드 정규화)** 입니다.

## 특수문자? combining characters?

위에서 특수문자라고 표현했던 문자는 유니코드에서 사용되는 Combining Characters(결합문자)입니다. 문자에 Combining Mark(결합문자)를 합하여 표현하는 방식으로 영어의 발음기호, 악센트, 베트남어, 독일어 등에서 사용합니다.

    e + ´ = é
    u + ¨ = ü

## a(65); != ã(227) 그러나 a == ã

&#65;의 유니코드 값은 ```65``` , &#771;의 유니코드 값은 ```771```, 그래서 &#227;의 유니코드 ```227```값은 입니다. 유니코드값 기준으로 보면 다르지만 데이터 처리 과정에서 동일하게 처리해야하는 경우도 있습니다. 예를 들어 사용자의 이름을 ```São Paulo```로 저장했습니다. 하지만 검색을 위해서는 ```São Paulo```와 ```Sao Paulo```가 모두 허용되어야 합니다. 이런 과정을 위해서 필요한 것이 유니코드 정규화입니다.

## Golang에서 Unicode Normalization

Golang에서는 유니코드 정규화를 위해 golang blog에서 [가이드](https://blog.golang.org/normalization)하고 있습니다. 

```
package main

import (
    "fmt"
    "unicode"

    "golang.org/x/text/transform"
    "golang.org/x/text/unicode/norm"
)

func isMn(r rune) bool {
    return unicode.Is(unicode.Mn, r) // Mn: nonspacing marks
}

func main() {
    t := transform.Chain(norm.NFD, transform.RemoveFunc(isMn), norm.NFC)
    result, _, _ := transform.String(t, "São Paulo, Brazil. Wien, Österreich.")
    fmt.Println(result)
}
```
[https://play.golang.org/p/6kyFQbz8Dov](https://play.golang.org/p/6kyFQbz8Dov)

## 결론

사용자에게서 제약 없는 문자를 입력 받을 때는 유니코드 정규화를 고려해야 합니다. 

## 참고

- [Golang 블로그의 유니코드 정규화](https://blog.golang.org/normalization)
- [stackoverflow에 올라온 유니코드 정규화 질문](https://stackoverflow.com/questions/26722450/remove-diacritics-using-go)
- [유니코드 내용 참조 블로그](https://miaow-miaow.tistory.com/36)
- [유니코드 내용 참조 ](https://velog.io/@leejh3224/%EB%B2%88%EC%97%AD-%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%8A%B8%EB%A7%81%EC%9D%84-%EB%85%B8%EB%A9%80%EB%9D%BC%EC%9D%B4%EC%A7%95-%ED%95%B4%EC%95%BC%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0)
- [다양한 combining mark](https://www.unicode.org/charts/PDF/U0300.pdf)
