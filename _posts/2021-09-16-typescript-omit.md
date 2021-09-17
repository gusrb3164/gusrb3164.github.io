---
layout: post
title: '타입스크립트 omit기능 이해하기'
description: Typescript의 특정 타입을 제거하여 재사용성을 높여주는 Omit에 대해서
categories: Typescript
tags: [Programming, Typescript]
---

## Omit

타입스크립트 인터페이스를 다루다 보면 특정 인터페이스를 사용하려고 하는데 정의된 속성을 모두 사용하지 않는 경우가 있다.
예를 들어, API요청으로 받는 속성들을 인터페이스로 정의했는데 특정 속성만 사용하려고 일부를 제외하는 경우이다.

이럴 때 Omit을 사용하면 인터페이스에서 일부 속성을 제외한 상태로 사용할 수 있다.

**Omit<T,K>** 에서 T는 사용할 타입이나 인터페이스, K는 사용하지 않을 속성이다.

EX)

```ts
interface Props {
  path: string;
  count: number;
  name: string;
}

type Tpath = Omit<Props, 'count'>;

const url: Tpath = {
  path: 'https://',
  name: 'main',
};
```

여러개의 속성을 없애고 싶다면 \| 연산자를 사용하면 된다.

EX)

```ts
interface Props {
  path: string;
  count: number;
  name: string;
}

type Tpath = Omit<Props, 'count' | 'name'>;

const url: Tpath = {
  path: 'https://',
};
```
