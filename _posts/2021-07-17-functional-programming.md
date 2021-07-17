---
layout: post
title: 함수형 프로그래밍이란
description: 함수형 프로그래밍의 본질과 명령형 프로그래밍과의 차이
categories: Computer-Science
tags: [Programming, Web]
---

## 프로그래밍의 방식

프로그래밍 패러다임은 프로그래밍을 어떠한 방식으로 하게 될지를 결정해주는 역할을 한다. 이러한 프로그래밍 패러다임의 주축은 크게 2가지 형태로 나뉜다.

- 명령형 프로그래밍 : 무엇(What)을 할 것인지 보다 어떻게(How) 할 것인지를 중심으로 프로그래밍 한다.

  - 절차지향 프로그래밍 : 수행되어질 순서대로 프로그래밍이 이루어지는 방식

  - 객체지향 프로그래밍 : 객체들의 단위로 프로그래밍이 이루어지는 방식

- 선언형 프로그래밍 : 어떻게(How) 할 것인지 보다 무엇을(What) 할 것인지를 중심으로 프로그래밍 한다.

  - 함수형 프로그래밍 : 순수 함수들을 조합해서 프로그래밍을 하는 방식

---

## 함수형 프로그래밍

기존의 명령형 프로그래밍은 기능을 구현하기 위한 코드가 모두 노출되어 있다 보니 규모가 큰 프로젝트의 코드는 어느새 너무 복잡하게 될 가능성이 높았다. 하지만 함수형 프로그래밍은 추상화된 함수들을 조합해서 선언적으로 프로그래밍 하다 보니 프로젝트 자체가 모듈화가 잘 되어 있어서 가독성과 유지보수 모두 뛰어나다.

> 함수형 프로그래밍을 클린 코드 책에서는 **대입문이 없는 프로그래밍**이라고도 부른다.

이러한 함수형 프로그래밍을 이루기 위한 핵심 개념들이 존재한다.

### 순수 함수

함수형 프로그래밍에서 뜻하는 함수들은 순수 함수를 의미한다. 여기서 순수 함수란 동일한 인자에 대해 항상 일관된 결과를 나타내야 하며, Side Effect가 없는 함수를 말한다. 즉, 함수의 실행이 외부에 영향을 끼치거나 받으면 안된다.

- Side Effect의 종류
  1. 함수 외부의 변수 값이 변경됨.
  2. 자료 구조를 제자리에서 수정함. (ex: 정렬된 값을 새로 반환하지 않고 해당 배열을 바로 수정함)
  3. 객체의 필드값을 설정함.
  4. 예외나 오류가 발생하며 실행이 중단됨.
  5. 콘솔 또는 파일 입출력이 발생함.

이러한 조건을 충족하는 순수 함수는 모듈화를 통해 뛰어난 재사용성과 높은 추상화 수준을 가진다.

### 1급 객체로서 함수

그리고 위의 순수 함수를 구현하기 위해 함수형 프로그래밍에서 함수는 1급 객체로 다뤄져야 한다.

- 1급 객체의 특징
  1. 변수에 함수를 할당할 수 있다.
  2. 다른 함수의 인자로 함수를 전달할 수 있다.
  3. 함수의 반환값으로 함수를 전달할 수 있다.

자바스크립트 언어는 함수가 위와 같은 특징들을 가지기 때문에 함수가 1급 객체로서 취급받고 함수형 프로그래밍 언어로 불리고 있는 것이다.