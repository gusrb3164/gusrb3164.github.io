---
layout: post
title: Lighthouse 개선 일지(1)
description: 사내 플랫폼 lighthouse 개선 1차과정
categories: Web
tags: [Programming, Web, Frontend]
---

사내 플랫폼이 갈수록 무거워지면서 네트워크 payload도 줄이고 사용자경험도 높일 필요성을 느껴서 lighthouse를 개선하는 과정을 회고하면서 앞으로도 계속 진행하는 과정을 기록하려고 합니다.

## 1차 문제점 JavaScript Bundle Size

![lighthouse-first](/assets/images/posts/lighthouse1.png)

초기 lighthouse의 지표는 특히 performance 지표가 낮았는데 우선 가장 빠르게 할 수 있는 js bundle을 쪼개는 과정이 필요했습니다.

![lighthouse-detail-first](/assets/images/posts/lighthouse-detail1.png)

> Reduce unused JavaScript가 거의 절반을 차지할 정도로 문제가 컸던걸 확인했던 지표

## javascript dynamic import

그래서 seo와 관련이 적으며 스크롤로 도달하는 지표가 낮았던 UI들을 intersection observer를 활용하여 dynamic import로 로드하는 방식을 도입했습니다.

그 결과 performance 점수가 평균6점정도 오른 것을 확인할 수 있었습니다.
![lighthoust-second](/assets/images/posts/lighthouse2.png)
![lighthoust-detail-second](/assets/images/posts/lighthouse-detail2.png)
Reduce unused JavaScript가 약 1.3초정도 감소했습니다. SEO가 상승했던 것은 아직 import되지 않은 element에서 있던 문제가 사라져서 오른 것으로 파악됩니다.

## 2차 문제점 Image asset

자바스크립트 관리는 이후에도 계속 필요하지만 그 다음 먼저 중요하다고 생각한 것은 이미지 resource를 줄이는 것이였습니다. 아무래도 이미지나 비디오가 전송 용량의 대다수를 차지하는 큰 형식이기 때문인데다 저희 플랫폼은 로드하는 이미지가 매우 많았기 때문입니다.


![network-image](/assets/images/posts/network-image.png)
> 사진에 나타나는 network리스트는 모두 메인 페이지에서 불러오던 image입니다.

## From jpg to webp

이미지용량 최적화를 위해 jpg를 webp로 변환하는 것을 우선 생각했습니다. 이미 이미지의 사이즈는 어느정도 적절하게 변환되었다고 생각하기 때문에 거의 동일한 품질에서 용량을 거의 절반가량 낮출 수 있는 webp형식을 사용하자고 생각했습니다.

> [jpg, webp, avif에 대한 이전 글](https://gusrb3164.github.io/web/2021/11/26/browser-image-optimzing/)

그래서 aws s3에 jpg를 webp로 변환하는 lambda를 붙여서 여러 경로에서 jpg를 업로드하면 자동으로 webp이미지까지 업로드 하고자 했습니다.

그 결과 사진들을 업로드하면 동일한 경로에 jpg와 webp 두가지 확장자를 소유하고 있으며 이 사진들을 picture element tag로 사용했습니다.

> webp를 아직 지원하지 않는 브라우저도 있기 때문에 picture 태그로 동적 이미지 서빙을 할 수 있습니다.

![lighthoust-third](/assets/images/posts/lighthouse3.png)

그 결과 image요청이 약 300KB정도 줄어들었고 performance 지표를 평균 42점 정도로 올릴 수 있었습니다.

고화질 사진을 반드시 제공해야 하는 것이 아니면 모바일 기준으로 webp이미지 적용은 괜찮은 선택지라고 생각합니다.

---

이후에 지표개선을 위해 생각하는 과정은 역시 JavaScript 최적화라고 생각해서 불필요한 소스코드를 줄이고 최대한 최적화를 해보려고 합니다. 아직 TreeShaking에 대해 미숙하지만 더 공부해서 적용해볼 생각입니다.

그리고 SEO는 100점을 목표로 개선할 생각입니다.