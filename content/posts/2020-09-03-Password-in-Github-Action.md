---
title: "Github Action을 이용한 빌드 & 실행에서 패스워드 숨기기"
date: 2020-09-03T14:35:00+09:00
draft: false
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

아마도 개발자는 위와 같은 단계를 반복적으로 수행하게 됩니다. 잊지 않고 반복적으로 해야하는 것도 중요하지만 사람이 직접 수행하다보면 실수도 하기 마련입니다.

그렇다면 golang 과 Github Action을 이용하여 반복적인 업무를 수행하는 코드를 만들어 보도록 합시다. 동시에 DB 접속정보(id, password, ip)와 같이 민감한 정보는 Secret 기능을 이용하여 코드 상에서 보이지 않도록 해봅니다.

## Postgresql DB의 특정 컬럼의 데이터를 업데이트 하는 golang code

대상 DB는 Postgresql DB를 사용합니다. 다른 DB를 사용한다면 그에 맞는 패키지를 사용해야 합니다.

- MySQL: https://github.com/go-sql-driver/mysql
- MSSQL: https://github.com/denisenkom/go-mssqldb
- Oracle: https://github.com/rana/ora
- Postgres: https://github.com/lib/pq
- SQLite: https://github.com/mattn/go-sqlite3
- DB2: https://bitbucket.org/phiggins/db2cli

필요에 따라 ORM을 사용해도 되지만, simple한 query만 사용하기에 기본 패키지에 있는 database/sql를 사용합니다.
소스코드의 기능은 아래와 같습니다.

1. 환경변수에 등록된 정보를 읽어온다.
2. DB 접속 정보를 구성하여 접속한다.
3. ```SELECT 1``` 테스트 query를 수행한다.
4. 등록된 사용자인지 확인한다.
5. 카드 번호를 hash 한 값을 DB에 업데이트 한다.

```
package main

import (
	"crypto/sha256"
	"database/sql"
	"fmt"
	"os"
	"time"

	_ "github.com/lib/pq"
)

func main() {
	fmt.Println("DCCafe User RFID Update")

	// input
	name := "홍길동"
	// input
	cardNumber := "3255213734"

	dbHost := os.Getenv("DB_HOST") // 로컬에서 개발 시에는 환경변수 DB_HOST에 등록
	dbPassword := os.Getenv("DB_PW") // 로컬에서 개발 시에는 환경변수 DB_PW에 패스워드 등록

	conn := "user=dcadmin password=%s host=%s dbname=dcadmin sslmode=disable"

	if len(name) == 0 {
		fmt.Printf("[Error] user empty\n")
		return
	}

	if len(cardNumber) == 0 {
		fmt.Printf("[Error] Card Number empty\n")
		return
	}

	if len(dbHost) == 0 {
		fmt.Printf("[Error] Not find DB HOST\n")
		return
	}

	if len(dbPassword) == 0 {
		fmt.Printf("[Error] Not find DB Password\n")
		return
	}

	db, err := sql.Open("postgres", fmt.Sprintf(conn, dbPassword, dbHost))
	if err != nil {
		panic(err)
	}
	defer db.Close()

	var status int
	err = db.QueryRow("SELECT 1").Scan(&status)
	if err != nil {
		panic(err)
	}

	if status != 1 {
		panic(fmt.Sprintf("SELECT 1 is not %d\n", status))
	}

	// get User
	var user Users
	err = db.QueryRow(`SELECT email, rfid, company, index, name, leave, regdate, updatedate FROM users WHERE name = $1`, name).Scan(&user.email, &user.rfid, &user.company, &user.index, &user.name, &user.leave, &user.regdate, &user.updatedate)
	if err != nil {
		panic(err)
	}

	fmt.Printf("find user %s\n", user.name)

	rfid := hash(cardNumber)

	status = 0
	err = db.QueryRow(`SELECT 1 FROM users WHERE rfid = $1`, rfid).Scan(&status)
	if err == nil {
		fmt.Printf("[Error] Exist Card Number:%s\n", cardNumber)
		return
	}

	fmt.Printf("Get new RFID %s\n", rfid)

	_, err = db.Exec(`UPDATE users SET rfid = $1, updatedate = now() WHERE index = $2`, rfid, user.index)
	if err != nil {
		panic(err)
	}

	fmt.Printf("Update User: %s, CardNumber: %s\n", user.name, cardNumber)
}

func hash(cardNumber string) string {
	sum := sha256.Sum256([]byte(cardNumber))
	return fmt.Sprintf("%x", sum)
}

// Users DB
type Users struct {
	email      string // PK
	rfid       string // PK
	company    string
	index      int
	name       string
	leave      bool
	regdate    time.Time
	updatedate time.Time
}
```
로컬에서 테스트하여 정상적으로 동작하는 소스코드는 gihub 프로젝트에 push 합니다. 

## Github Action에서 cron / build / execute 스크립트

자동 실행을 위한 Github Action용 스크립트를 작성합니다.
Github Action을 사용하기 위해서는 아래의 위치에 yml로 작성한 스크립트가 필요합니다. 

```
github-프로젝트-root/.github/workflows/main.yml
```

스크립트는 수행 시점을 정의하는 ```on```과 수행해야하는 것(환경구성, 소스코드 실행)을 정의하는 ```job```로 구분됩니다.

아래와 같이 ```on```을 정의하면 두가지 조건을 설정할 수 있습니다. 

- ```master``` 브런치에 push가 되면 스크립트를 실행
- 일요일 0시에 스크립트를 실행

```
on:
  push:
    branches: [ master ]
  schedule:
    - cron:  '0 9 * * SUN'
```

그리고 Github Action 스크립트는 소스코드와 달리 github site에서 직접 수정하는 것이 도움이 됩니다. github site의 에디터에서 직접 수정을 하면 cron의 경우 아래와 같이 시간에 대한 정보를 확인할 수 있습니다.

![](https://sh0seo.github.io/images/cron-preview.png)

전체 스크립트는 아래와 같습니다.

```
name: update

on:
  push:
    branches: [ master ]
  schedule:
    - cron:  '0 9 * * SUN'
      
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2.3.2
      with:
        ref: "master"
    
    - name: go setup
      uses: actions/setup-go@v2.1.2
      with:
        go-version: "1.13"
    
    - name: pg driver
      run: |
        go get github.com/lib/pq
    - name: run 
      run: |
        go run main.go
      env:
        DB_HOST: ${{ secrets.DB_HOST}}
        DB_PW: ${{ secrets.DB_PW}}
```

## 환경정보는 secrets에 저장

소스코드를 실행하기 위해 필요한 DB 접속정보는 공개되면 안되기 때문에 환경설정에서 읽어서 처리하도록 합니다. Github Action에서 환경설정에 특정 값을 설정하기 위해서는 ```env```에 설정을 합니다. 아래 스크립트의 의미는 secrets.DB_HOST에 설정된 값을 DB_HOST 환경변수에 저장하겠다는 의미입니다.

```
      env:
        DB_HOST: ${{ secrets.DB_HOST}}
        DB_PW: ${{ secrets.DB_PW}}
```

그렇다면 ```${{ secrets.XXXXX }}``` 값은 어디에서 설정하면 될까요? github의 해당 프로젝트의 setting 에서 관리할 수 있습니다. 

![](https://sh0seo.github.io/images/secrets-value.png)

## 결론

Github Action 기능을 이용하여 golang 소스코드를 실행하는 기능을 만들었습니다. Travis CI에서 처리했던것 보다는 좀 더 github 친화적이라서 사용하기도 편하고, 특별한 서버 없이도 수행할 수 있는 장점이 있습니다. 저 같은 경우는 cron 기능, DB 업데이트 등을 모두 golang + github action으로 처리하고 있습니다. 좋은 기능은 역시 자주 사용해서 잊어먹지 않아야하고 이렇게 공유해야 합니다. ^^;

## 참조

- [Golang DB Package](http://golang.site/go/article/106-SQL-DB-%ED%99%9C%EC%9A%A9)
- [Golang sql](https://pkg.go.dev/database/sql?tab=doc)
- [Github Action](https://docs.github.com/en/actions)
