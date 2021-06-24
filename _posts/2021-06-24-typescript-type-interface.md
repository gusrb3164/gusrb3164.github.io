---
layout: post
title: '타입스크립트 type과 interface의 차이'
description: Typescript의 타입 선언 방식중 type과 interface의 차이와 적절한 사용법을 이해하자
categories: Typescript
tags: [Programming, Typescript]
---

타입 별칭(Type Alias)과 인터페이스(Interface)는 타입에 이름을 지어주는 수단으로 거의 비슷한 기능을 수행한다.
아래의 Sushi 별칭을 사용한 모든 곳에 type이나 interface를 사용할 수 있다.

```ts
type Sushi = {
  calories: number;
  salty: boolean;
  tasty: boolean;
};
interface Sushi {
  calories: number;
  salty: boolean;
  tasty: boolean;
}
```

하지만 몇가지 다른점이 존재한다.

## 선언 방식

타입 별칭은 더 일반적이어서 타입 별칭의 오른편에는 타입 표현식을 포함한 모든 타입이 등장할 수 있다. 하지만 인터페이스의 오른편에는 반드시 형태가 나와야 한다.

```ts
type A = number;
type B = A | string;
// interface는 이와 같은 형태로 작성할 수 없다.
```

## 동적 확장

인터페이스는 상속할 때 상속받는 인터페이스의 타입에서 상위 인터페이스를 할당할 수 있는지 확인한다. 하지만 타입은 확장 타입을 최대한 조합하는 방향으로 동작한다. 따라서, 인터페이스가 해당 부분에서는 보다 정확한 에러 검출이 가능하다.

```ts
interface A {
  good(x: number): string;
  bad(x: number): string;
}
interface B extends A {
  good(x: string | number): string;
  bad(x: string): string; // 상위 타입에서 number를 바꿀 수 없으므로 에러가 발생한다
}

type A = {
  good(x: number): string;
  bad(x: number): string;
};
type B = A & {
  good(x: string | number): string;
  bad(x: string): string; // 상위 bad함수를 오버로드하는 방식으로 상황에 맞게 타입을 제공하므로 에러가 발생하지 않는다.
};
```

## 선언 합침

타입 별칭은 선언 이후에 const 처럼 타입을 변경할 수 없지만, 인터페이스는 같은 이름으로 정의된 여러 인터페이스를 자동으로 확장한다.

```ts
interface User {
  name: string;
}
interface User {
  age: number;
}
let a: User = {
  name: 'foo',
  age: 30,
};

type User2 = {
  name: string;
};
// 중복된 식별자 error발생
type User2 = {
  age: number;
};
```

---

결론지어보면 type alias 선언의 방식이 자유롭지만 union타입이나 tuple타입으로 선언할 시 확장이 자유롭지 못하고 interface는 선언의 형태가 정해져 있지만 확장에서 자유로우면서 보다 정확하게 에러 검출을 할 수가 있다. 따라서 type alias는 union과 tuple 혹은 세부적인 리터럴 타입(원시값)을 선언할 때 적절하고 그 외에 객체의 형태와 같은 부분에서는 interface가 적절하다고 생각한다.
