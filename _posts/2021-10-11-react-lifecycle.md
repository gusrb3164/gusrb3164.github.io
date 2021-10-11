---
layout: post
title: 리액트 컴포넌트 생명주기
description: 클래스형 컴포넌트 관점과 함수형 컴포넌트 관점 각각에서 주로 사용되는 react component의 lifecycle
categories: Web
tags: [Programming, Web, Frontend]
---

리액트의 모든 컴포넌트는 **생명주기 메서드**를 가지며, 이 메서드를 오버라이딩하여 특정 시점에 코드가 실행되도록 설계할 수 있다.

**함수형 컴포넌트**에서는 이 메서드들을 모두 대체하지는 않지만 리액트에서 핵심 메서드들을 대체할 수 있는 기능을 제공한다.

생명주기는 크게 **마운트, 업데이트, 언마운트** 순으로 볼 수 있다. 그리고 추가로 오류를 핸들링하는 메서드도 존재한다.(오류 핸들링은 현재 클래스형 컴포넌트에서만 다룰 수 있다.)

## 마운트 (Mount)

---

컴포넌트의 인스턴스가 생성되어 DOM 상에 삽입될 때 실행된다.

- 클래스형 컴포넌트

  - constructor() : 선언 초기에 메서드를 바인딩하거나 관리하는 state를 초기화하는 생성자
  - render() : 클래스 컴포넌트에서 jsx를 반환하는 메서드 (필수)
  - componentDidMount() : 컴포넌트가 마운트된 직후 (초기 데이터 fetching에 적합)

- 함수형 컴포넌트
  - return : 함수를 선언하고 return 하는 과정으로 constructor, render 메서드를 대신할 수 있다.
  - useEffect : 초기 Mount된 직후 useEffect 내부의 로직은 무조건 한번 실행되게 된다.

## 업데이트 (Update)

---

props 또는 state가 변경되면 실행된다.

- 클래스형 컴포넌트

  - render()
  - componentDidUpdate() : 컴포넌트 업데이트가 일어난 직후에 호출된다. 최초에는 호출되지 않음 (상태의 변화에 따른 데이터 fetching에 적합)

- 함수형 컴포넌트
  - return
  - useEffect(deps) : useEffect의 deps 배열 내부에 갖고있는 상태를 저장함으로써 해당 상태가 변경될때마다 호출된다.

## 언마운트 (Unmount)

---

- 클래스형 컴포넌트
  - componentWillUnmount() : 컴포넌트가 제거되기 직전에 호출된다. 마운트하면서 가지고 있던 타이머나 소켓 이벤트, 등을 해제할 때 주로 사용한다.
- 함수형 컴포넌트
  - useEffect(clean up) : useEffect내의 return 함수인 cleanup 에서 componentWillUnmount를 대체할 수 있다.

## 오류 처리

- 클래스형 컴포넌트
  - static getDerivedStateFromError(error) : 하위의 자손 컴포넌트에서 오류가 발생했을 때 호출된다. 매개변수로 오류를 전달받으며, 갱신된 state 값을 반드시 반환해야 한다. (render 단계에서 호출되므로, 부수 효과를 발생시키면 계속 호출되는 문제가 발생할 수 있다.)
  - componentDidCatch(error, info) : getDerivedStateFromError와 같이 자손 컴포넌트에서 오류가 발생했을 때 호출되지만 어떤 컴포넌트가 오류를 발생시켰는지에 대한 정보인 info 인자를 추가로 전달받는다. (commit 단계에서 호출되므로 부수 효과를 발생시켜도 된다. 주로 sentry로의 오류 로그 전송에 사용된다.)
- 함수형 컴포넌트
  - X

> 오류 처리에 관해서는 클래스형 컴포넌트인 ErrorBoundary를 상위 컴포넌트에 선언함으로써 에러 처리 로직을 분담할 수 있다. 이 부분에 대해서는 나중 글에서 확인할 수 있다.

---

> 참고문서 : <https://ko.reactjs.org/docs/react-component.html>
