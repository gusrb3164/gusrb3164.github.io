---
layout: post
title: 타입스크립트 React.FC 사용법
description: 리액트 함수에 타입스크립트 React.FC 사용하기
tags: [Programming, frontend]
---

타입스크립트에서는 각 클래스, 함수, 변수마다 형을 지정해줄 수 있는데 React Function 에는 React.FC 라는 형을 지정할 수 있다.

```js
import React from 'react';

type GreetingsProps = {
	name: string,
};
// function component 예시
const Greetings: React.FC<GreetingsProps> = ({ name }) => (
	<div>Hello, {name}</div>
);

export default Greetings;
```

이러한 React.FC 를 사용하면 우선 props 에 기본적으로 children 이 들어가있다는 장점이 있다. 그리고 넘어오지 않아도 되는 값이 있을 경우에는 type 변수명 뒤에 ? 이라는 형식을 추가하는 것도 명심하자.

```js
type GreetingsProps = {
	name: string,
	text?: string, // optional 로 형식을 준다.
};
```

그리고 아래 코드처럼 defaultProps를 지정해 주고 사용할 수도 있다.

```js
Greetings.defaultProps = {
	text: '!',
};
```
