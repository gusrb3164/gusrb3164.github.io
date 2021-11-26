---
layout: post
title: 브라우저 이미지 최적화하기 - WebP - AVIF - JPEG
description: picture 태그를 이용해 사용자의 브라우저 별로 지원하는 최신의 이미지 포맷 기술에 해당하는 이미지만 제공하여 요청 크기를 줄이고 서버를 절약해보자
categories: Web
tags: [Programming, Web, Frontend]
---

개발하는 과정에서 JPG나 PNG 포맷으로 된 이미지만 그냥 다루다가 점점 프로젝트의 규모가 커지고 한번에 전송하는 이미지도 많아지다 보니 이미지 최적화의 필요성을 느끼게 됐다.

---

## 이미지 크기를 왜 줄여야 하는가

---

- 사용자가 웹 페이지에 접속하면 html, css, js 파일들을 받게 되고 요즘은 js 파일의 용량을 줄이는 노력도 많이 있다. 하지만 이미지의 크기는 코드 데이터보다 훨씬 크기 때문에 요청하는 이미지의 용량을 줄이는 것이 가장 먼저 해야할 일이자, 사용자 경험을 가장 빨리 개선할 수 있다.

- aws s3와 같은 클라우드 버킷에 이미지를 올려놓고 관리하는 경우에는 저장 공간보다는 **서버에서 클라이언트에게 데이터를 전송하는 용량**에 따라 금액 차이가 주로 나기 때문에 이러한 서버 비용을 크게 절약할 수 있다.

---

## 브라우저에서 주로 지원하는 이미지 형식

---

1. JPEG

   - JPEG/JFIF는 웹 상에서 사진 등의 화상을 보관하고 전송하는 데 가장 널리 사용되는 파일 형식이다. 하지만 이 압축 방법은 문자, 선, 세밀한 격자 등 고주파 성분이 많은 이미지의 변환에서는 GIF나 PNG에 비해 불리하며, 나쁜 품질을 보이는 경우가 많다.

   - 지원 브라우저 : Chrome, Edge, Firefox, IE, Opera, Safari, Samsung Browser (사실상 모든 브라우저의 버전에서 지원한다)

2. WebP

   - 인터넷의 웹페이지에서 널리 사용되고 있는 JPEG을 대체하기 위해 제시된 형식이다. JPEG 등과 같은 손실 압축 포맷으로 화질 저하를 최소화 하면서 파일 크기를 축소(같은 품질의 JPEG 이미지의 10 ~ 80 % 정도까지 압축) 하였다.

   - 지원 브라우저 : Chrome, Edge, Firefox, Opera, Safari (주요 브라우저에서 대부분 지원하지만 사파리는 아직 미지원 하는 버전도 상당히 존재한다.)

3. AVIF

   - 이미지와 애니메이션 포맷에 높은 효율을 자랑하는 형식이다. PNG나 JPEG보다 훨씬 더 나은 압축 기능을 제공하며 더 높은 색 깊이, 애니메이션 프레임, 투명도 등을 지원한다.

   - 지원 브라우저 : Chrome, Opera, Samsung Browser 들의 최신 버전

### 형식별 용량 비교

현재 갖고있는 JPEG이미지를 WebP와 AVIF 형식으로 변환해 비교해보았다.

![capture](/assets/images/posts/2021-11-26/size-compare.png)

JPEG형식이 133KB인 이미지에 대해서 WebP가 78KB로 약 **42%**, AVIF가 약 **52%**의 압축률을 보여준다. 실제 웹 브라우저에서도 이정도의 이미지 크기가 주로 전송될 텐데, 절반의 크기 축소는 매우 큰 차이이다.

---

## 이미지 로드 최적화하기

---

그러면 각 사용자가 이용하는 브라우저를 확인하고 이에 맞는 이미지를 지원하기 위해 자바스크립트로 이미지 src의 확장자를 적절하게 수정하는 코드를 작성해야 하는가?

다행히도 **html picture tag**로 이러한 분기를 대체할 수 있다.

> picture : img 요소의 다중 이미지 리소스(multiple image resources)를 위한 컨테이너를 정의할 때 사용한다.

아래의 예시처럼 위에서부터 최적의 이미지 source를 picture 태그 안에 순서대로 넣어주고 가장 마지막에 지원하는 버전인 jpg를 img 태그에 넣어주면 된다.

```html
<picture>
  <source srcset="img-url/large.avif" type="image/avif" />
  <source srcset="img-url/large.webp" type="image/webp" />
  <img src="img-url/large.jpg" alt />
</picture>
```

여기서 모바일 브라우저를 고려해서 사진 크기도 분기에 추가해서 최적화 하고 싶다면 media 속성을 추가한 분기를 넣어주면 된다.

```html
<picture>
  <source srcset="img-url/small.avif" type="image/avif" media="(max-width:960px)" />
  <source srcset="img-url/large.avif" type="image/avif" />
  <source srcset="img-url/small.webp" type="image/webp" media="(max-width:960px)" />
  <source srcset="img-url/large.webp" type="image/webp" />
  <source srcset="img-url/small.jpg" media="(max-width:960px)" />
  <img src="img-url/large.jpg" alt />
</picture>
```

아래 링크에서 레진코믹스 개발자님께서 만든 예시를 확인하면 실제로 결과를 확인할 수 있다.
<https://codepen.io/naradesign/pen/rNjbqGb?editors=1100>

브라우저 도구로 Current source를 확인해 보면 304px 의 width에서 avif를 지원하는 크롬 브라우저를 쓰기 때문에 small 버전의 avif 형식의 이미지를 요청한 것을 확인할 수 있다.

![capture](/assets/images/posts/2021-11-26/picture-result.png)

---

> 참고자료
>
> - <https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture>
> - <https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types>
