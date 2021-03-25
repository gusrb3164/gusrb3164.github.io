---
layout: post
title: 자바스크립트 호이스팅의 개념
description: 자바스크립트의 특징중 하나인 호이스팅 이해하기
tags: [Programming, javascript]
---

# 호이스팅 이란?

-   호이스팅(Hoisting) : 함수와 var로 선언한 변수는 자바스크립트 Parser가 가장 상단에 있는 것처럼 인식하는 것. 이를 잘 못 이해해서 변수 및 함수 선언이 물리적으로 작성한 코드의 상단으로 옮겨지는 것으로 이해할 수 있지만 이는 틀리다. 변수 및 함수 선언은 컴파일 단계에서 메모리에 저장되지만, 위치는 코드에서 입력한 위치와 정확히 일치한 곳에 존재한다.

-   호이스팅 대상 : var 변수 선언, 함수 선언문

    => let, const 변수 선언과 함수 표현식은 호이스팅이 일어나지 않는다.

이해를 돕기 위해 MDN [문서](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)를 통한 예시코드를 살펴보면

```js
catName('Chloe');

function catName(name) {
	console.log("My cat's name is " + name);
}
/*
위 코드의 결과는: "My cat's name is Chloe"
*/
```

위의 코드는 catName 이 뒤늦게 선언 됐기 때문에 오류가 날 줄 알았지만 실행이 잘 된다. 그 이유는 자바스크립트가 함수 선언문에 대한 코드 순서를 신경쓰지 않기 때문이다.

-   이러한 특징 때문에 코드의 가독성과 유지보수를 위해 호이스팅이 일어나지 않도록 하려면 어떻게 해야할까?

    -   함수와 변수를 가급적 코드 상단부에서 선언하면, 호이스팅으로 인한 스코프 꼬임 현상을 방지할 수 있다.
    -   호이스팅에 해당되지 않는 let/const 를 주로 사용한다. (eslint-airbnb 에서도 권장하는 방식)
