---
layout: post
title: node js 와 mysql 비밀번호 연동 오류 해결하기
description: mysql 8버전 방식에 맞춘 비밀번호 연동 오류 해결
tags: [Programming, Backend, DB]
---

node js 실습을 진행하면서 mysql 사용하는 아래의 코드를 실행하는데 자꾸 실행이 안되는 것이다.

```js
var mysql = require('mysql');

var client = mysql.createConnection({
	host: 'localhost',
	user: 'root',
	password: '1234',
	database: 'company',
});

client.connect((err) => {
	if (err) {
		console.log(err);
	}
});

client.query('SELECT * FROM products', function (error, result, fields) {
	if (error) {
		console.log('쿼리 문장에 오류가 있습니다.');
	} else {
		console.log(result);
	}
});
```

그래서 로그에 나타나는 오류를 출력해보니

```
 Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client
 ```

 라고 떠서 Stack Overflow 정보를 확인해보니 이전 node js 에서 이용하는 mysql 라이브러리와 현재 mysql 8.0 이상의 비밀번호 정책이 다르다고 한다.

 현재 mysql 은 sha256으로 암호정책이 업그레이드 돼서 같은 비밀번호를 입력하지만 라이브러리가 해싱하는 암호와 mysql 이 해싱하는 암호가 일치하지 않았던 것이다.

 그래서 스택오버플로에서 해결법을 찾아보니 사용하고 있는 mysql 암호정책을 다시 예전 것으로 바꿔주기만 하면 되는것이다. (물론 더 안 좋은 암호정책이므로 실습용으로만 사용하는게 좋겠다.)

그래서 아래의 명령어를 mysql 커맨더로 입력하고 다시 실습코드를 실행하니 정상적으로 실행이 된다.

 ```
 ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '내 비밀번호';

 flush privileges;
 ```

## 자세한 해결방법

<https://stackoverflow.com/questions/50093144/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server/56509065>
