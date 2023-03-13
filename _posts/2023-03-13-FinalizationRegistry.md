---
layout: post
title: javascript의 FinalizationRegistry을 활용한 메모리 디버깅
description: 자바스크립트의 가비지 컬렉터의 수집 시점을 디버깅할 때 활용할 수 있는 FinalizationRegistry API
categories: Web
tags: [Programming, javascript]
---

javascript에는 [FinalizationRegistry](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/FinalizationRegistry) 라는 API를 통해서 객체가 가바지 컬렉터에 수집될 것을 등록됐을 때 호출받을 **callback**을 등록할 수 있습니다.

## 사용 방식

---

보통 WeakRef와 같은 기능을 사용할 때 cleanup callback 으로도 활용되지만, 개발 중에 garbage collected 시점을 확인해보고 싶을 때 이용해보는 것도 유용하다고 생각합니다.

| [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/FinalizationRegistry#avoid_where_possible)에서는 FinalizationRegistry 을 cleanup callback 방식으로 너무 의존해서 사용하는 것을 가급적 피하라고 합니다. GC의 동작 방식이 자바스크립트 엔진, 버전에 따라 다를 수도 있으므로 자신이 원하는대로의 동작이 이루어지지 않을 수도 있으며 이는 오히려 부작용이 생길 수 있기 때문입니다.

## 디버깅 방법

---

FinalizationRegistry만 사용한다고 해서 당장 메모리상에서 callback호출을 예상하는 시점에 기대하지는 못할 수도 있습니다. javascript 엔진은 메모리상 여유가 있을 때 기다리고 있다가 메모리의 일괄 처리가 필요할 때 수행할 수도 있기 때문입니다. 그래서 브라우저 상에서 개발자 도구의 메모리 탭에서 휴지통 아이콘(Collect garbage)를 클릭해서 강제로 실행시켜 디버깅을 할 수 있습니다.

![chrome collect garbage](/assets/images/posts/2023-03-13/collect-garbage.png)

예를 들어 아래의 코드를 보시면 클로저를 활용해 전역 변수에 값을 할당하고 있습니다. 이 코드를 브라우저상에서 실행 시키고 Collect garbage를 수행하시면 log에는 아무 영향이 없음을 확인할 수 있습니다.

```js
const registry = new FinalizationRegistry(message => console.log(message));

function test() {
  const foo = {};
  registry.register(foo, 'foo is removed');

  return () => {
    return foo;
  };
}

const bar = test()();
```

하지만 아래의 코드처럼 할당받은 객체를 전역 변수에서 지워준 다음 Collect garbage를 수행하면 "foo is removed" 의 log가 찍히는 것을 확인할 수 있습니다.

```js
const registry = new FinalizationRegistry(message => console.log(message));

function test() {
  const foo = {};
  registry.register(foo, 'foo is removed');

  return () => {
    return foo;
  };
}

let bar = test()();
bar = null;
```

![collect garbage log](/assets/images/posts/2023-03-13/result.png)

이와 같은 방식으로 클로저의 동작 방식을 직접 확인해보거나 모듈내의 변수나 컴포넌트 내의 값이 언제 가비지 컬렉터에 수집되는지 직접 테스트하며 확인할 수 있습니다.
