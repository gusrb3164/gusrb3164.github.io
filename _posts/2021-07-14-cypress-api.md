---
layout: post
title: Cypress 테스트에 쓰이는 주요 기능들
description: cypress에 쓰이는 주요 api에 대한 기능
categories: Web
tags: [Programming, Web, Frontend, Test]
---

## 주요 기능

- cy.visit()
  - 200 status code와 함께 html 파일을 로드한다.
- cy.request()
  - 서버가 존재하는지 확인하고 http 요청을 수행한다.
- cy.contains()
  - 특정 content 가 포함된 DOM 객체를 얻어온다.
- cy.get()
  - DOM의 속성을 통해서 특정 DOM 객체를 얻어온다. (querySelector와 유사)
- .find()
  - 해당 DOM 내의 속성을 찾는다.
- .type()
  - 타이핑이 가능한 DOM에 값을 입력한다.
- .click()
  - click action이 가능한 DOM의 click event를 수행한다
- .its()
  - 얻어온 객체 값의 속성을 찾는다. (ex: http요청으로 받아온 response body 내의 값을 검사)
