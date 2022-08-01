---
layout: post
title: 재미삼아 해본 bun.js node.js 성능 비교 테스트
description: beta 버전이지만 해외에서 유명한 javascript runtime 유명한 bun.js의 성능 비교
categories: Web
tags: [Programming, javascript]
---

요즘 node.js를 대체하기 위해 bun.js 가 떠오르고 있습니다.

<https://bun.sh/> 에 가보면 **Bun is a fast all-in-one JavaScript runtime** 라는 문구가 가장 먼저보이죠.

공식문서에 나온 설명들을 읽어보면 bundler, Web API, typescript, jsx, sqllite 등 자바스크립트 환경에서 요즘 필요로 하는 기본적인 기능들을 자체적으로 제공하고 있습니다. **all-in-one** 이라는 문구가 적합하다고 느껴지네요.

fast 라는 단어는 궁금해서 직접 간단하게 테스트 해보았습니다.

```sh
실행 환경
node 16.14.2v
bun 0.1.5v
```

## Test 1

첫 번째로 hash를 통해 연산성능을 테스트 해보았습니다.

```js
const crypto = require('crypto');
const code = 'wqeieqwhid90wq90j2190je3ijeodw';

console.time('crypto');

for (let i = 0; i < 1000000; i++) {
  crypto.createHash('sha256').update(`code${i}`).digest('hex');
}

console.timeEnd('crypto');
```

![hash테스트](/assets/images/posts/bun-crypto.png)

crypto의 경우는 10배 가량의 차이로 node가 월등히 빨랐습니다. 이정도로 차이나는것은 연산 자체의 성능 보다는 아무래도 내부 최적화 이슈가 있는 것 같네요.

## Test 2

두 번째로 api fetch를 테스트 해봤습니다.

```js
import fetch from 'node-fetch';

const baseURL = 'https://api.sampleapis.com/baseball';

(async () => {
  console.time('fetch');

  await fetch(`${baseURL}/hitsSingleSeason`);
  await fetch(`${baseURL}/hitsCareer`);
  await fetch(`${baseURL}/eraSingleSeason`);
  await fetch(`${baseURL}/eraCareer`);
  await fetch(`${baseURL}/stolenBasesSingleSeason`);
  await fetch(`${baseURL}/stolenBasesCareer`);
  await fetch(`${baseURL}/battingAvgsSingleSeason`);

  console.timeEnd('fetch');
})();
```

![hash테스트](/assets/images/posts/bun-fetch.png)

fetch의 경우도 node가 1.5배정도 더 빠른 성능을 보였습니다. node-fetch가 아닌 bun 자체의 fetch를 사용했을 때도 동일한 결과를 보였습니다.

---

간단한 2개의 테스트를 통해선 node에 비해 저조한 성능을 보였습니다.

추가적으로 bun에서 강점을 보인다는

- Server-side rendering React
- Load a huge table

2가지의 경우도 다음에 테스트 해봐야겠네요.

아직 beta 버전이라 정식 출시는 멀었지만, all-in-one이라는 큰 장점을 가지고있는 런타임 환경인만큼 성능까지 node를 앞선다면 향후의 게임체인저가 될 수 있을거란 생각이 듭니다.

마지막으로 아래의 영상은 bun에 대해 잘 요약되어있어서 추천드립니다.

<iframe width="718" height="300" src="https://www.youtube.com/embed/FMhScnY0dME" title="JavaScript just got way faster" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>