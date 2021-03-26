---
layout: post
title: node js를 시작하기 위해 알아야 할 동기, 비동기
description: 동기, 비동기 방식에 대한 개념과 node js 에서의 사용법
categories: Web
tags: [Programming]
---

자바스크립트 관련 개념을 공부할 때 많이 나오는 개념중 하나가 동기와 비동기이다.

보통 프로그래밍을 공부할때 주로 깔려있는 방식이 동기 방식이고 서버 프로그래밍쪽은 대부분 비동기 관련 방식이다.

> 동기 (synchronous) : 받은 요청을 처리하는 동안 다른 일을 할 수가 없습니다. 요청한 결과가 돌아올 때까지 기다려야 합니다.

> 비동기 (Asynchronous) : 받은 요청을 처리하는 동안 다른 일을 할 수가 있습니다. 요청한 결과가 돌아오기 전에 다른일을 먼저 할 수 있습니다.

```js
console.log('1');
console.log('2');
console.log('3');
```

위의 코드의 실행결과는 당연히 순서대로 실행한 결과가 나오는 동기 방식이다.

```
1
2
3
```

```js
console.log('1');
setTimeout(() => {
	console.log('2');
}, 0);
console.log('3');
```

그런데 위의 실행 결과는 1,3,2 순서로 나오게 된다.

```js
1;
3;
2;
```

setTimeout 자체가 비동기방식으로 내장된 함수라서 시간을 0으로 설정해도 다른 쪽에 실행하라고 요청해놓고 3을 출력하는 작업을 수행하므로 2를 출력하는 작업은 조금 늦게 도착할 수 있기 떄문이다.

이러한 비동기 방식을 고려하지 않고 데이터를 받아오는 함수를 생각해보자.

```js
const printData = function () {
	api.get('https://example.com/', function (response) {
		data = response;
	});
	return data;
};
```

위의 코드에서 데이터를 받아오는걸 생각할 때, data 에는 response가 저장되어 return 하는걸 기대하지만 data가 response에서 받아오는 동안 비동기 방식으로 return 을 실행하게 되면 undefined를 return 하게 되는게 비동기 방식에서 실수하는 흔히 발생하는 문제점이다.

이러한 문제를 해결하기 위해 해당 비동기 함수 안에 매개변수로 함수를 주는 콜백함수를 사용하는 방식이 있는데, 콜백의 구조가 중첩될수록 복잡해지고 이해하기 어려운 콜백지옥이라는 문제가 생길 수 있어 자바스크립트에서는 async, await 이라는 방식으로 주로 비동기 방식을 다루기도 한다.
