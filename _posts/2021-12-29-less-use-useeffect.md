---
layout: post
title: useEffect를 남용하지 말자 - react
description: 사이드 이펙트를 유발시키는 useEffect를 남용하면 안되는 이유와 useEffect를 줄이기 위한 방법
categories: Web
tags: [Programming, Web, Frontend]
---

## useEffect

리액트 hook을 통해 개발하다 보면 useEffect의 사용은 생명주기를 다루는데 있어서 필수적인 기능이다. 그래서 나도 참조하고 싶은 상태가 변할때 효과를 준다던가, 첫 렌더링 시점 이후에 비동기 호출을 한다던가 할 때 자주 사용한다.

하지만 useEffect는 말 그대로 참조하는 deps가 변경되면 로직이 실행되는 side effect를 발생시킨다.

> Effect Hook을 사용하면 함수 컴포넌트에서 side effect를 수행할 수 있습니다. - React 공식 문서

## useEffect의 부작용

side effect는 본래 목적 외의 상황이 발생하는 것이다. 따라서 useEffect를 사용할수록 발생하는 side effect를 관리해야 하는 것을 의미한다.

그래서 useEffect를 남용하게 된다면 결국 side effect가 많이 발생하게 되고, 그러다가 side effect에서 발생하는 효과가 다른 useEffect의 deps를 변경시키게 되면 연쇄적인 효과가 발생해서 점점 관리하기 어려워진다.

## useEffect를 효과적으로 관리하기

1. useEffect에서 수행하는 역할은 단일 책임의 원칙에 의하여 직관적이게 하자
2. callback함수에서 외부 값을 참조해야 할 때 useEffect로 우회하지 말자
3. 비동기 fetching을 위한 useEffect는 hook과 관련한 라이브러리를 사용하거나 custom hook으로 만들어서 sideEffect를 해당 상태만 참조하도록 선언적으로 사용하자.
4. 기능을 설계할 때 useEffect를 최소한으로 사용하는 방식을 우선적으로 생각하자.

계속...
