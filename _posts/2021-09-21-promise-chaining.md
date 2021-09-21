---
layout: post
title: 자바스크립트 Promise Chaining
description: 순차적인 비동기 처리를 위한 프라미스 체이닝 기법
categories: Web
tags: [Programming, javascript]
---

비동기 요청을 한 뒤에 받은 token 값을 다음 비동기 요청에 담아서 보내야하는 순차적 비동기 요청을 생각해 보자. 이러한 경우에는 Promise Chaining 기법을 통해 구현할 수 있다.

## Promise Chaining

아래는 프라미스 체이닝을 통해 비동기 요청을 순차적으로 보내는 예시이다. 세개의 비동기 요청을 순차적으로 처리하고 있다.

```js
loadScript('/article/promise-chaining/one.js')
  .then(function (script) {
    return loadScript('/article/promise-chaining/two.js');
  })
  .then(function (script) {
    return loadScript('/article/promise-chaining/three.js');
  })
  .then(function (script) {
    // 불러온 스크립트 안에 정의된 함수를 호출해
    // 실제로 스크립트들이 정상적으로 로드되었는지 확인합니다.
    one();
    two();
    three();
  });
```

만약 비동기 요청의 개수가 매 순간마다 달라지기 때문에 동적으로 chaining을 해야할 때는 loop를 통해 chaining을 구현한다.

예를 들어, 배열의 값을 1초마다 순차적으로 출력해야 한다고 가정해보자.
아마 배열의 길이만큼 loop마다 promise를 실행한다면 1초뒤에 모든 값들이 출력될 것이다. 순차적으로 하는 가장 간단한 방법은 async await을 쓰는 것이라 생각한다. 각 실행되는 함수를 resolve 되기 전까지 await 하고 있다가 resolve가 되면 다음 순서를 실행하는 방식이다.

```js
async function execLoop() {
  const arr = [1, 2, 3, 4, 5];
  for (const i of arr) {
    await new Promise(resolve =>
      setTimeout(() => {
        console.log(i);
        resolve();
      }, 1000),
    );
  }
}

execLoop();
```

---

> 참고문서 : <https://ko.javascript.info/promise-chaining>
