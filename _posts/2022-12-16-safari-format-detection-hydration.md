---
layout: post
title: safari에서만 발생하는 react hydration 오류 - format-detection
description: mobile safari의 meta format-detection 기능으로 인한 hydration 오류 디버깅 과정.
categories: Web
tags: [Web, Frontend]
---

react 18로 migration하면서 next.js를 쓰는 프로젝트는 server와 client의 dom이 일치하도록 만들어서 **hydration issue**가 발생하지 않도록 더욱 신경써야 합니다. 앞으로 server component를 사용하게 되면 dom은 server에서 렌더링하는 것을 기반으로 client에 그대로 노출되기 때문입니다.

사내 프로젝트도 migration과정에서 sentry에 로깅되는 hydration 오류들을 대부분 해결을 했으나 자꾸 **모바일 사파리 브라우저**에서만 hydration 오류가 발생하고 있었습니다.

## 분석

왜 그런지 devtools로 ReactDom.hydrate(root)에 전달되는 root의 childNodes를 실제 node들과 비교해 봤습니다.

- server에서 렌더링하는 nodes

  ```jsx
  <p>
      <br />
      test : name name test : 111-11-11111
  </p>
  ```

- client에서 실제 childNodes에 담기는 값

  ![debugging capture](https://user-images.githubusercontent.com/34272561/206841771-51468139-7f18-4813-9b2d-68c5b5dd129f.png)

둘을 비교해보면, 숫자 text를 safari 브라우저에서 \<a> tag로 감싸버려서 server와 client간의 dom이 차이가 나는 hydration 이슈가 발생하는 것을 확인할 수 있습니다.

## format-detection

그래서 파악해보니 모바일 safari에서는 **format-detection**이란 기능으로 인해 이메일, 전화번호, 주소, 날짜의 형태를 자동으로 링크처리를 하고 있습니다.

이러한 자동 처리는 meta tag의 format-detection 설정을 통해 끌 수 있습니다.

```html
<meta name="format-detection" content="telephone=no">
<meta name="format-detection" content="date=no">
<meta name="format-detection" content="address=no">
<meta name="format-detection" content="email=no">
```

---

당장 이렇게 해결할 순 있지만 이 문제는 개발자에 의해 발생하는 것이 아닌 hydration 오류이므로 서버사이드
프레임워크에서도 해결해야 할 문제로 보여 [next.js issue](https://github.com/vercel/next.js/issues/43914)에도 등록을 한 상태입니다.

> 참고 문서 <br/>
> <https://stackoverflow.com/questions/28027330/html-email-ios-format-detection>
