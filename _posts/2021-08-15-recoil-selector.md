---
layout: post
title: Recoil에서 selector를 통한 비동기 호출
description: 리코일 셀렉터를 이용한 비동기 호출과 이에 대한 로딩중 처리를 지원하는 Suspense를 사용해보자
categories: Web
tags: [Programming, Web, Frontend]
---

## 리액트의 데이터 fetching

기존에 리액트 컴포넌트에서 비동기 데이터 fetching은 대부분 useeffect 내부에서 이루어졌다.
그래서 useeffect의 코드 뿐만 아니라 useeffect 에서 fetching하는 동안의 상태를 해당 컴포넌트에 추가로 작성해야하는 복잡함이 많았다.

## Recoil과 Suspense를 통한 데이터 fetching

하지만 suspense를 지원하는 recoil과 함께 사용하면 마치 컴포넌트 내부의 코드를 동기와 유사하게 작성하면서 비동기 fetching을 깔끔하게 할 수 있다.

```jsx
import { selector, useRecoilValue } from 'recoil';

// 비동기 처리를 하는 recoil selector
const myQuery = selector({
  key: 'MyDBQuery',
  get: async () => {
    const response = await fetch(getMyRequestUrl());
    return response.json();
  },
});

// 비동기 fetching을 해야하는 리액트 컴포넌트
function QueryResults() {
  const queryResults = useRecoilValue(myQuery);

  return <div>{queryResults.foo}</div>;
}

// 비동기 fetching중인 컴포넌트를 Suspense로 감싸면서 fallback에 로딩중일 때 나타낼 컴포넌트를 넣는다.
function ResultsSection() {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <QueryResults />
    </React.Suspense>
  );
}
```

위에서 데이터 fetching을 해야하는 QueryResults 컴포넌트에서는 그냥 동기적 데이터를 가져오듯이 useRecoilValue를 호출함으로써 매우 간결하게 비동기 처리를 수행하고 있다.

여기서 만약 selector에 데이터 fetching에 필요한 query 인자들을 주고 싶다면 selectorFamily를 이용한 커링 방식을 사용하면 된다.

```jsx
import { selectorFamily, useRecoilValue } from 'recoil';

// selectorFamily가 인자로 query를 받아서 데이터를 가져온다
const myQuery = selectorFamily({
  key: 'MyDBQuery',
  get: query => async () => {
    const response = await fetch(getMyRequestUrl(query));
    return response.json();
  },
});

// 비동기 fetching을 해야하는 리액트 컴포넌트 myQuery는 selectorFamily이므로 인자를 전달할 수 있다.
function QueryResults(query) {
  const queryResults = useRecoilValue(myQuery(query));

  return <div>{queryResults.foo}</div>;
}

// 비동기 fetching중인 컴포넌트를 Suspense로 감싸면서 fallback에 로딩중일 때 나타낼 컴포넌트를 넣는다.
function ResultsSection() {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <QueryResults />
    </React.Suspense>
  );
}
```

---

> 참고문서 : <https://recoiljs.org/ko/docs/api-reference/core/selector/>
