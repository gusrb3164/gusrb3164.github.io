---
layout: post
title: Throttle, Debounce 방식의 개념과 특징
description: 웹에서의 스로틀과 디바운스 기법의 개념과 차이점, 자바스크립트를 활용하여 구현방식에 대해 이해하기.
categories: Web
tags: [Programming, javascript]
---

## Throttle과 Debounce를 사용하는 이유

API 요청이나 페이지를 렌더링 하는 경우는 오버헤드가 크기 때문에 과하게 많이 일어날 경우 제한을 걸어두는것이 좋다. 예를 들어, 브라우저 크기를 조정할 때마다 resize 함수가 실행되고 DOM을 다시 그린다고 가정하자. 보통 resize는 조금만 크기를 조절해도 resize가 초당 50번 가량 일어날 수가 있으며 50번 모두 DOM을 렌더링 한다면 성능 저하가 크게 발생할 것이다.

이러한 경우에 이러한 무거운 연산을 반복적으로 실행하는 것에 대해 제약을 걸어 제어할 수 있는 수준으로 이벤트를 발생시키기 위해 사용하는 방식이 Debounce와 Throttle이다.

## Throttle

Throttle은 일단 이벤트가 발생하면 일정 시간이 지나기 전까지 다시 호출되지 않도록 하는 것이다.
즉, 1초의 제한을 걸어 두었다면 맨 처음 이벤트가 발생하고 나서 1초동안은 이벤트가 발생해도 해당 함수를 실행하지 않는 것이다.

이러한 방식은 우선 사용자가 계속 이벤트를 발생시킬 때 실행을 지속적으로 시키는 것이므로 무한 스크롤링 페이지나 브라우저를 resize 하는 경우에 적합하다.

```js
function throttle(fn, delay) {
	let timer;
	return function () {
		if (!timer) {
			timer = setTimeout(() => {
				timer = null;
				fn.apply(this, arguments);
			}, delay);
		}
	};
}
```

## Debounce

Debounce는 연이어 발생하는 이벤트들 중 일정 시간을 초과하는 마지막 이벤트에 대해서만 해당 함수를 실행하는 방식이다. 즉, 순차적으로 발생하는 이벤트를 하나로 **그룹화**를 함으로써 그룹 단위로 실행을 하는 것이다.

이러한 방식은 사용자의 검색을 자동 지원할 때 연속적인 타이핑에 대해서 마지막 결과만 검색 요청을 하면 되므로 적절하다.

```js
function debounce(fn, delay) {
	let timer;
	return function () {
		clearTimeout(timer);
		timer = setTimeout(() => {
			fn.apply(this);
		}, delay);
	};
}
```

---

> 참고 사이트: <https://webclub.tistory.com/607>
