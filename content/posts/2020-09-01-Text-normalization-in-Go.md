---
title: "Text normalization in Go"
date: 2020-09-01T04:15:00+09:00
draft: true
tags: go
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

- Text-normalization-in-Go.md https://blog.golang.org/normalization
- https://stackoverflow.com/questions/63681731/how-to-convert-utf8-characters-to-ascii-analogues
- https://stackoverflow.com/questions/26722450/remove-diacritics-using-go
