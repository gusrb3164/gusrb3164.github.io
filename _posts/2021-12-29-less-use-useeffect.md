---
layout: post
title: useEffect를 남용하지 말자 - react
description: 사이드 이펙트를 유발시키는 useEffect를 남용하면 안되는 이유와 useEffect를 줄이기 위한 방법
categories: Web
tags: [Programming, Web, Frontend]
---

## useEffect

---

리액트 hook을 통해 개발하다 보면 useEffect의 사용은 생명주기를 다루는데 있어서 필수적인 기능이다. 그래서 나도 참조하고 싶은 상태가 변할때 효과를 준다던가, 첫 렌더링 시점 이후에 비동기 호출을 한다던가 할 때 자주 사용한다.

하지만 useEffect는 말 그대로 참조하는 deps가 변경되면 로직이 실행되는 side effect를 발생시킨다.

> Effect Hook을 사용하면 함수 컴포넌트에서 side effect를 수행할 수 있습니다. - React 공식 문서

## useEffect의 부작용

---

### 1. useEffect간의 연쇄 작용

useEffect를 1개 추가한다면 고려해야 할 side effect는 1개가 된다. 근데 useEffect가 늘어날 수록 고려해야 할 경우의 수는 선형적으로 늘어나지 않고 연쇄반응 때문에 관리해야 할 side effect는 기하급수적으로 늘어날 수 있다.

아래의 예시는 한개의 상태가 변할 때 발생하는 sideEffect만 고려하면 된다.

```jsx
import React, { useState, useEffect } from 'react';

const App = () => {
  const [state, setState] = useState();

  useEffect(() => {
    // do something...
  }, [state]);

  // return ...
};
```

그런데 아래의 예시에서 각각의 useEffect도 하나의 sideEffect를 발생 시키지만, 해당 sideEffect가 실행되면서 다른 useEffect를 유발시키기 때문에 무한루프로 리렌더링을 유발시키게 된다.

```jsx
import React, { useState, useEffect } from 'react';

const App = () => {
  const [foo, setFoo] = useState(0);
  const [bar, setBar] = useState(0);

  useEffect(() => {
    setBar(prev => prev + 1); // 해당 비동기 로직이 실행되면 아래의 useEffect가 실행된다
  }, [foo]);

  useEffect(() => {
    setFoo(prev => prev + 1); // 해당 비동기 로직이 실행되면 위의 useEffect가 실행된다
  }, [bar]);

  // return ...
};
```

그래서 useEffect가 여러개 사용된다면 다른 useEffect에 영향을 끼치는 것까지 고려해야 한다.

### 2. 흐름 추적의 어려움

보통 함수는 다른 함수에서 호출을 하거나 사용자와의 상호작용을 통해 callback방식으로 호출된다. 그래서 실행흐름을 발생한 첫 이벤트나 함수부터 따라간다면 파악하기가 쉽다.

하지만 useEffect는 deps에서 참조하는 상태들이 변하는 경우에 함수가 실행이 된다. 즉, 실행흐름만을 따라가다 보면 놓칠 수 있는 로직이기 때문에 나중에 유지보수할 때 이 useEffect는 어떤 흐름에서 파생되어 만들었는지 파악하기 어렵고, 디버깅도 어려울 수 있다.

아래의 예시는 onClick 이벤트가 발생하면 submit 함수가 실행되는 것까지는 예측할 수 있다. 하지만 sideEffect로 useEffect가 실행되는 것은 foo의 상태가 변한다는 것을 프로그래머가 useEffect들의 deps를 따로 확인해야 파악할 수 있다.

```jsx
import React, { useState, useEffect } from 'react';

const App = () => {
  const [foo, setFoo] = useState(0);

  const submit = () => {
    setFoo(prev => prev + 1);
  };

  useEffect(() => {
    // do something...
  }, [foo]);

  return <button onClick={submit}>submit<button/>
};
```

위의 예시는 단순한 로직이라 파악하기는 쉽지만 거대해진 코드에서는 아무래도 인간인지라 놓칠 수 있는 흐름이 점차 많아지게 된다.

---

useEffect는 react hook에서 lifecycle의 역할을 보완해주는 매우 유용한 hook이다. 그리고 clean up 기능도 제공하기 때문에 함수 내에서 side effect를 다루는 것보다 훨씬 관리하기도 쉽다.

하지만 이러한 편리함에 익숙해져 남용하다 보면 **side effect**가 걷잡을 수 없이 커져서 유지보수가 어렵거나 새로운 기능을 추가할 때 버그가 발생할 확률도 높아지게 되므로 최소한으로 써야한다.
