---
layout: post
title: 자바스크립트 foreach와 for의 동기 비동기
description: js foreach 사용할 때 for와 다르게 주의할 점
categories: Web
tags: [Programming, Backend]
---

서버 api를 작성하던중 이러한 로직을 작성하게 되었다.

```js
let newQuizIds = [];
quizs.forEach(async quiz => {
  const tmp = await new quizModel(quiz).save();
  newQuizIds.push(tmp._id);
});
console.log(newQuizIds);
```

분명 newQuizIds log를 출력하면 push된 값들이 나와야하는데 [] 이렇게 빈 배열만 나오는 것이다. 그래서 몇시간동안 찾아보니,

### foreach 는 배열 요소를 순차적으로 돌면서 callback을 실행할 뿐, callback이 끝날때 까지 기다렸다가 다음 callback을 실행하는 것이 아니라고 한다. 즉, foreach 자신이 실행하는 코드가 비동기를 하든 안하든 관심이 없이 넘어간다는 것이다

그래서 newQuizIds 배열에 값이 담기기도 전에 log 출력문이 실행되는 것이였다.

> 생각해보면 async/await 함수를 순차적으로 실행하려면 함수를 호출하는 곳도 async/await 로직으로 구현되어야 하는데 foreach 내부 동작 방식은 그렇지 않기 때문에 당연한 것이다.

---

## 그러면 어떻게 해결할 까?

이를 해결하기 위해 나는 foreach와 달리 순차처리로 진행되는 for나 for of로 작성하기로 했다.

```js
let newQuizIds = [];
for (let quiz of quizs) {
  const tmp = await new quizModel(quiz).save();
  newQuizIds.push(tmp._id);
}
console.log(newQuizIds);
```

위의 코드는 newQuizIds 에 값이 정상적으로 들어온 것이 확인된다.

---

### 그런데, 내가 작성한 로직은 순차처리가 옳다고 생각해서 이렇게 작성했는데 병렬처리를 원하는 사람은 어떻게 풀어야할까 생각해봤다

이럴 땐 foreach 대신 map을 사용하고 모든 병렬처리가 끝날때까지 기다려야 하므로 Promise.all 을 사용하는게 바람직하다고 한다.

병렬처리 코드 예시

```js
async function parallel(array) {
  const promises = array.map(url => async_download(url));
  await Promise.all(promises);
  //위의 병렬처리가 끝나야 log출력이 실행된다.
  console.log('all done :)');
}
```
