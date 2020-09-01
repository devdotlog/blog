---
title: "Text normalization in Go"
date: 2020-09-01T04:15:00+09:00
draft: true
tags: go, unicode, normalization, 유니코드, 정규화
---

아래와 같이 특수 문자가 섞여 있는 문자열이 있다.

```"São Paulo, Brazil. Wien, Österreich."```

이런 문자열을 아래와 같이 알파벳으로 변경하고 싶다면 어떻게 해야 할까?

```"Sao Paulo, Brazil. Wien, Osterreich."``` 

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

참고

- https://www.google.com/search?q=Unicode+%EA%B8%80%EC%9E%90+%EC%A4%91+%EC%9D%BC%EB%B6%80%EB%8A%94+%ED%95%A9%EC%9E%90(combining+characters)&oq=Unicode+%EA%B8%80%EC%9E%90+%EC%A4%91+%EC%9D%BC%EB%B6%80%EB%8A%94+%ED%95%A9%EC%9E%90(combining+characters)&aqs=chrome..69i57.256j0j7&sourceid=chrome&ie=UTF-8
- https://miaow-miaow.tistory.com/36
- https://velog.io/@leejh3224/%EB%B2%88%EC%97%AD-%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%8A%B8%EB%A7%81%EC%9D%84-%EB%85%B8%EB%A9%80%EB%9D%BC%EC%9D%B4%EC%A7%95-%ED%95%B4%EC%95%BC%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0
- Text-normalization-in-Go.md https://blog.golang.org/normalization
- https://stackoverflow.com/questions/63681731/how-to-convert-utf8-characters-to-ascii-analogues
- https://stackoverflow.com/questions/26722450/remove-diacritics-using-go
