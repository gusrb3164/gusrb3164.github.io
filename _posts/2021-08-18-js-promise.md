---
layout: post
title: 자바스크립트 Promise에 대해서
description: 자바스크립트 콜백 비동기 처리를 대신해주는 프라미스에 대해서
categories: Web
tags: [Programming, javascript]
---

## Promise

promise는 비동기 처리에 대해 반드시 결과를 반환하는 로직을 가진 기능이다. 그리고 이에 대한 결과는 resolve, error 둘중 하나로 귀결된다. 즉, promise를 통해 비동기 처리를 수행하면 성공을 처리하는 로직인 resolve, 실패를 처리하는 로직인 error 두가지 경우로 나눠서 예상을 하고 설계를 하게 된다.

따라서 resolve를 수행도중에 error를 처리하거나, error를 처리하는 도중에 resolve를 처리할 수는 없다. 한가지 경우를 처리하게 되면 다른 한가지 경우는 반드시 무시된다.

## Promise와 콜백

Promise는 우선 자연스러운 흐름에 따라 비동기 처리를 가능하게 해준다.

promise.then().catch() 문법을 통해서 요청에 대한 응답이 올 때의 처리를 순서적으로 코드를 작성할 수가 있다.

이전의 콜백 방식에서는 미리 콜백 함수를 준비해야 했으며, 콜백함수가 여러번 중첩이 되는 순간 바로 복잡해지는 문제가 있었는데, Promise를 사용하면서 많이 해결될 수 있다.
