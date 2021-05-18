---
layout: post
title: 자바스크립트 map, foreach 100% 구현하기
description: 자바스크립트 prototype을 이용하여 배열 map, foreach 기능을 똑같이 구현해보고 해당 기능을 지원하지 않는 브라우저에 대처해보자.
categories: Web
tags: [Programming, javascript]
---

예전 글에서 자바스크립트의 map과 forEach 함수에 대해 정의를 한적이 있다. 하지만 당시에 prototype개념을 모른 상태라서 이번에 정확히 구현해보자.

---

## forEach 구현하기

```js
Array.prototype.myforEach = function (callback, context) {
	for (let i = 0; i < this.length; i++) {
		callback.call(context || null, this[i], i, this);
	}
};

[1, 2, 3].myforEach((data, idx, array) => {
	console.log(`data: ${data}, idx: ${idx}, array: ${array}`);
});
// result
// data: 1, idx: 0, array: 1,2,3
// data: 2, idx: 1, array: 1,2,3
// data: 3, idx: 2, array: 1,2,3
```

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 문서의 foreach 함수 설명에 따르면 각 배열마다 실행할 callback 함수를 받으며, 해당 객체가 참조할 this context를 선택적으로 받는다.

그리고 callback 함수에서 사용될 인자들은 해당 데이터, 해당 데이터의 인덱스, 그리고 전체 배열이 있다.

이러한 기능을 파악한뒤 Array 객체의 프로토타입에 myforEach라는 직접 제작한 함수를 할당하면 된다.

참고로 this context를 call 메서드를 이용해서 해당 함수를 호출하며, this가 undefined가 되면 문제가 생기기 때문에 context||null 구문을 추가해서 할당하지 않을 때는 null값을 주도록 설정했다.

> call에 대한 정보 <https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call>

---

## map 구현하기

```js
Array.prototype.mymap = function (callback, context) {
	const arr = [];
	for (let i = 0; i < this.length; i++) {
		arr.push(callback.call(context || null, this[i], i, this));
	}
	return arr;
};
console.log([1, 2, 3].mymap((data, idx, array) => `data: ${data}, idx: ${idx}, array: ${array}`));
// result
// ["data: 1, idx: 0, array: 1,2,3", "data: 2, idx: 1, array: 1,2,3", "data: 3, idx: 2, array: 1,2,3"]
```

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 문서의 map 함수 설명에 따르면 각 배열마다 실행할 callback 함수를 받으며, 해당 객체가 참조할 this context를 선택적으로 받는다. foreach와 다른점은 **각 배열마다 실행한 결과값을 return**한다는 점이다.

그 외에 전달받는 인자와 함수 내에 사용되는 인자들은 foreach와 동일하다. 동일하게 call로 함수를 호출하는 방식을 이용했으며 arr배열에 결과들을 push해서 return하는 구조이다.
