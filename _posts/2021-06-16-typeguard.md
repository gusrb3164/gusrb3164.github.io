---
layout: post
title: 타입스크립트 Type Guard 에 대해 이해하기
description: Typescript에서 타입 가능성을 좁혀주는 타입 가드에 대해서 알아보기
categories: Web
tags: [Programming, Typescript]
---

## Type Guard

타입이 여러가지로 추론되는 경우에 특정 타입에 대해 단언함으로써
런타임에서의 타입 검사를 컴파일러에게 알려주는 기능

## 내장 타입가드 활용

기본적으로 자바스크립트에 내장된 typeof 와 instanceof를 활용하면 타입을 명시적으로 단언할 수 있다.

```ts
function doSomething(x: number | string) {
  if (typeof x === 'string') {
    console.log(x.substr(1));
    // if 문 내에서의 x는 string이라는 타입이 확실하다는 보장이 있으므로 string 의 substr 함수도 바로 나타낼 수 있게 된다.
  }
  // if 문 바깥에서는 number인지 string인지 확실치 않으므로 substr을 사용할 경우에 타입스크립트 오류가 발생한다.
}
```

## in,is 를 활용한 타입가드 활용

위의 자바스크립트가 제공하는 기능 외에 커스텀하여 활용하는 방식으론 is와 in 키워드를 사용할 수 있다.

```ts
interface A {
  x: number;
}
interface B {
  y: string;
}

function doStuff(q: A | B) {
  if ('x' in q) {
    // q: A
  } else {
    // q: B
  }
}
```

```ts
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

/**
 * arg를 Foo라고 타입 가드를 선언하는 is로 시작하는 함수 선언
 */
function isFoo(arg: any): arg is Foo {
  return arg.foo !== undefined;
}

function doStuff(arg: Foo | Bar) {
  if (isFoo(arg)) {
    console.log(arg.foo); // OK
    console.log(arg.bar); // Error!
  } else {
    console.log(arg.foo); // Error!
    console.log(arg.bar); // OK
  }
}
```
