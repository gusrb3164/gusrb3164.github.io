---
layout: post
title: 자바스크립트 map, filter 직접 구현해보기
description: 자바스크립트 배열 map, filter 기능 직접 구현해보기
tags: [Programming, javascript]
---

자바스크립트에 배열을 다루는 기본 함수중에 map과 filter 가 있다.

해당 함수들을 직접 구현해보는 것은 자바스크립트 기본 문법들을 익히는데 좋은 연습이 된다고 하고, 면접에서도 물어볼 수 있으니 연습해보는게 좋다.

---

# Map 구현하기

우선 map 부터 살펴보자, map 에 해당하는 기능들을 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 문서로 살펴보니

```js
arr.map(callback(currentValue[, index[, array]])[, thisArg])
```

arr 라는 배열에 콜백함수의 인자로 각 배열의 값(필수 조건), 해당 값의 인덱스, 그리고 OnlyRead로 다룰 수 있는 map()을 호출한 배열을 선택 인자로 줄 수가 있다.
그리고 thisArg 는 callback을 실행할 때 this로 사용되는 값이다.

여기서 thisArg 를 제외한 모든 기능을 구현해보자.

## 구현 코드

```js
nums = [1, 2, 3, 4, 5];

nums.map((num, i, arr) => console.log(num * 2, i, arr));

console.log('-----');
// 직접 map을 구현한 함수
const customMap = (array, func) => {
	const result = [];
	for (let i = 0; i < array.length; i++) {
		result.push(func(array[i], i, array));
	}
	return result;
};

customMap(nums, (num, i, array) => console.log(num * 2, i, array));
```

## 실행결과

```js
// 기본으로 내장된 map으로 출력한 결과
2 0 [ 1, 2, 3, 4, 5 ]
4 1 [ 1, 2, 3, 4, 5 ]
6 2 [ 1, 2, 3, 4, 5 ]
8 3 [ 1, 2, 3, 4, 5 ]
10 4 [ 1, 2, 3, 4, 5 ]
-----
// 직접 구현한 map으로 출력한 결과
2 0 [ 1, 2, 3, 4, 5 ]
4 1 [ 1, 2, 3, 4, 5 ]
6 2 [ 1, 2, 3, 4, 5 ]
8 3 [ 1, 2, 3, 4, 5 ]
10 4 [ 1, 2, 3, 4, 5 ]
```

### nums 라는 샘플 데이터를 처리해보니, 위의 결과가 똑같이 해당 값, 인덱스, map을 사용한 배열을 출력한다

---

# Filter 구현하기

filter에 해당하는 기능도 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 에서 살펴보니

```js
arr.filter(callback(element[, index[, array]])[, thisArg])
```

element는 처리할 현재 요소로 이름만 다를 뿐 나머지 인자들은 map 과 같은 방식이다.

여기서 thisArg 를 제외한 모든 기능을 구현해보자.

## 구현 코드

```js
nums = [1, 2, 3, 4, 5];

console.log(nums.filter((num) => num > 2));

console.log('-----');

const customFilter = (array, func) => {
	const result = [];
	for (let i = 0; i < array.length; i++) {
		func(array[i], i, array) ? result.push(array[i]) : null;
	}
	return result;
};

console.log(customFilter(nums, (num) => num > 2));
```

## 실행결과

```cpp
// 기본으로 내장된 filter로 출력한 결과
[ 3, 4, 5 ]
-----
// 직접 구현한 filter로 출력한 결과
[ 3, 4, 5 ]
```

### nums 라는 샘플 데이터를 각 값이 2보다 큰 값인 것만 log로 출력하도록 처리해보니, 결과가 똑같이 3, 4, 5 가 나왔다.
