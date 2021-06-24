---
layout: post
title: '[타입스크립트 프로그래밍] 타입스크립트에 대해 이해하기'
description: Typescript를 사용하는 이유와 Typescript에서 타입의 계층도를 알아보자
categories: Typescript
tags: [Programming, Typescript]
---

> O'REILLY 의 타입스크립트 프로그래밍의 책을 읽고 쓰는 글.

## Typescript

타입스크립트는 자바스크립트의 기본적인 기능에 정적 타입의 기능을 추가해주는 언어이다. 즉, 타입을 지정하지 못해서 발생하는 실수를 방지해주며, 자신과 미래의 개발자들에게 문서화를 제공하고, 리팩토링을 쉽게 만들며, 단위 테스트의 숫자를 반으로 줄임으로써 더 **안전한** 프로그램을 구현할 수 있게 보장한다.

여기서 '안전한'은 타입 안정성을 뜻한다.

> 타입 안정성 : 타입을 이용해 프로그램이 유효하지 않은 작업을 수행하지 않도록 방지한다.

---

## Typescript가 타입을 검사하는 방법

타입스크립트는 자바스크립트가 인터프리터 방식으로 런타임 실행을 하기전에 컴파일러가 코드를 바이트코드 대신 자바스크립트 코드로 변환하면서 컴파일 타임에 타입을 실행해주는 방식으로 실현된다.

즉, 최종적으로는 자바스크립트가 런타임 실행하는 것은 동일하지만 이전의 방식에 새로운 기능을 추가를 해주는 차이점이 있다.

![typescript-programming](/assets/images/posts/ts-concept.jpg)

---

## 기존 자바스크립트와 타입스크립트의 차이점

| 타입 시스템 기능            | 자바스크립트   | 타입스크립트        |
| --------------------------- | -------------- | ------------------- |
| 타입 결정 방식              | 동적           | 정적                |
| 타입이 자동으로 변환되는가? | O              | X(대부분)           |
| 언제 타입을 확인하는가?     | 런타임         | 컴파일 타임         |
| 언제 에러를 검출하는가?     | 런타임(대부분) | 컴파일 타임(대부분) |

---

## 타입 계층도

정적인 타입을 지정하기 위해 타입스크립트에서 주로 사용하는 타입들의 계층도는 아래와 같다.

![typescript-programming](/assets/images/posts/ts-types.jpg)

---

> 참고 서적 : 타입스크립트 프로그래밍