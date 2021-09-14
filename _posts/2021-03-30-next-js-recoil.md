---
layout: post
title: next js 에 recoil 적용하기
description: next js에 상태관리 툴인 recoil을 적용하는 과정
categories: Web
tags: [Programming, Web, Frontend]
---

## next js 탐색

react에서 SSR(Server Side Rendering)을 하게 도와주는 next js를 도입하여 프로젝트를 해보기로 했다.

여기서 문제는, 기존의 react는 CSR(Client Side Rendering) 특성상 모든 파일을 한꺼번에 받아오므로 상태관리 툴인 redux, recoil 혹은 context api 를 활용한 MVVM 구조 작성에 적응이 이미 완료된 상태였다.

하지만 Next js 는 말 그대로 페이지 별로 서버에 요청을 하는 구조이므로 ViewModel 과 같은곳에서 한꺼번에 라우팅 하는 방법을 모르는 상태였다.

그래서 찾아보니 next js의 모든 page는 렌더링을 거치기 전에 \_app.js 를 거친다고 한다.

즉, 각 해당 페이지의 상위에 \_app.js 의 root 레이아웃이 존재한다. 그리고 해당 app.js 는 자유롭게 커스터마이징이 가능하다.

---

## \_app.js 구조

가장 기본적인 app.js 의 구조는 다음과 같다.

```js
function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}

export default App;
```

여기서 오는 인자들중 Component 는 각 페이지 컴포넌트들이 오게 되고, pageProps는 getInitialProps(next js의 서버사이드 실행 로직) 를 통해 내려 받은 props들을 나타낸다.

---

## recoil 적용하기

이제 해당 Component 코드를 RecoilRoot 로 연결 시켜주기만 하면 된다.

```js
import { RecoilRoot } from 'recoil';

function App({ Component, pageProps }) {
  return (
    <RecoilRoot>
      <Component {...pageProps} />
    </RecoilRoot>
  );
}

export default App;
```

적용이 잘 되는지 확인하기 위해 기본적인 예시를 만들어보자.

1. states.js 생성 : 우선 recoil 상태 관리 요소 atom 을 이용해 기본 값을 설정해준다.

```js
import { atom } from 'recoil';

const nameState = atom({
  key: 'nameState',
  default: '',
});

export { nameState };
```

2. about.js 페이지를 생성하고 적용 : about 페이지에서 useRecoilState를 사용해 states.js 의 nameState 객체를 불러오고 input 의 입력에 따라 name 데이터가 갱신되는지 확인한다.

```js
import Layout from '../components/Layout';
import Link from 'next/link';
import { useRecoilState } from 'recoil';
import { nameState } from '../components/states';
import React from 'react';

const About = () => {
  const [name, setNameState] = useRecoilState(nameState);

  const updateName = e => {
    setNameState(e.target.value);
  };

  return (
    <Layout>
      <h1>Profile</h1>
      <p>Hello, {name}</p>

      <input
        type="text"
        name="name"
        id="input_name"
        onChange={updateName}
        placeholder="Enter your name"
      />

      <Link href="/">Back to main</Link>
    </Layout>
  );
};

export default About;
```

![result](/assets/images/posts/recoil-test.JPG)

input 값의 변경에 따라 값이 잘 변경되는걸 확인할 수 있다.

---

아래의 링크에서도 next js에서 recoil을 사용하는 법이 잘 나타나 있다.
<https://medium.com/swlh/how-to-use-recoil-js-library-in-next-js-framework-33af174dae68>
