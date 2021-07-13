---
layout: post
title: Cypress E2E 테스트를 리액트에 시작하기
description: 리액트에 cypress 테스팅 라이브러리를 적용하기 위한 준비 과정과 간단한 테스트 시작해보기
categories: Web
tags: [Programming, Web, Frontend, Test]
---

현재 진행하는 프로젝트의 프론트엔드의 UI를 클라이언트 입장에서 테스트 하기 위해 알아보던 중 기존의 Selenium 기반 테스트보다 새로 떠오르는 오픈소스 Cypress 가 워낙 문서화도 잘 되있다 보니 적용하기 위해 공부해 보았다.

## 준비 과정

우선 연습삼아 진행해 볼 리액트 프로젝트를 생성하고 cypress를 다운 받아 준다.

```sh
npx create-react-app cypress-test
yarn add cypress --dev # 혹은 npm install cypress --save-dev
```

그리고 cra를 통해 만들었으니 package.json 에 test 명령어로 리액트 테스트 라이브러리를 실행하도록 되어 있는데, 해당 부분을 사용하지 않으므로 cypress 실행 명령어로 바꿔주었다.

```json
{
  "scripts": {
    "test": "cypress open"
  }
}
```

---

## 실행

yarn test를 통해 cypress를 실행하면 루트 폴더에 cypress 폴더와 cypress.json 파일이 처음에 자동적으로 생성되면서 자신의 브라우저를 통해 cypress 테스트 창이 열리게 된다.

![capture](/assets/images/posts/cypress1.png)

테스트 파일들이 미리 몇개 자동적으로 생성되 있는데 해당 파일들은 자동적으로 생성된 cypress/integration 폴더 내의 파일들이다. cypress는 default적으로 integration 폴더 내부의 파일들을 인식해서 테스트를 진행한다.

그래서 해당 integration내의 파일들을 모두 지우면 cypress 테스트 브라우저도 인식을 해서 테스트케이스들이 모두 지워져 있는 것을 확인할 수 있다.

해당 파일들을 지우고 integration 폴더 내에 sample_spec.js 라는 간단한 테스트 케이스를 작성해보자.

```js
describe('My First Test', () => {
  it('Does not do much!', () => {
    expect(true).to.equal(true);
  });
}); // expect의 인자 값이 equal의 인자 값과 같으면 테스트가 통과하는 로직
```

![capture](/assets/images/posts/cypress2.png)

실제로 해당 파일을 저장하면 cypress 브라우저에서 바로 sample_spec.js 테스트케이스가 생겨나고 클릭을 해보면 테스트를 실행하고 통과하는 것을 확인할 수 있다.

이번엔 실제 페이지 렌더링을 테스트하기 위해 visit 함수를 이용해 cypress 페이지 접속 테스트를 진행해본다. visit함수에 자신의 로컬 url을 넣어준다면 자신의 로컬 환경을 테스트 할 수 있다.

```js
describe('My First Test', () => {
  it('Visits the Kitchen Sink', () => {
    cy.visit('https://example.cypress.io');
  });
});
```

- 참고 : eslint를 사용하는 환경에서는 cy에 lint를 발생시키게 되는데 cra로 생성한 package.json 내에 해당 extends를 넣어주게 되면 에러가 발생하지 않는다.

```json
"eslintConfig": {
  "extends": [
    "plugin:cypress/recommended"
  ]
}
```

---

## 리액트 테스트

이제 실제 리액트를 테스트 해보자. 그런데 자신의 로컬환경을 테스트 하려면 로컬 서버를 실행해야 하므로 테스트 하는 경우마다 리액트 서버를 실행시킨 상태에서 해야하는 불편함을 느낄 수 있다. 이런 경우에 cypress는 beforeEach라는 함수를 통해 테스트가 실행되기 전에 실행할 스크립트 명령어를 입력할 수 있다.

```js
describe('The Home Page', () => {
  beforeEach(() => {
    cy.exec('yarn start');
  });

  it('successfully loads', () => {
    cy.visit('http://localhost:3000');
  });
});
```

이제 테스트를 하게 되면 visit 내부의 경로를 자주 설정하게 될텐데 게속 http부터 입력하는건 번거로울 수가 있다.

그래서 초기에 생성된 cypress.json에 baseUrl을 설정해주면 이런 부분을 생략할 수가 있다.

```json
{
  "baseUrl": "http://localhost:3000"
}
```

cypress.json에 이렇게 설정해주면 방금 전의 url을 /로 생략할 수 있다.

```js
describe('The Home Page', () => {
  beforeEach(() => {
    cy.exec('yarn start');
  });

  it('successfully loads', () => {
    cy.visit('/');
  });
});
```

![capture](/assets/images/posts/cypress3.png)
해당 페이지를 성공적으로 띄우면서 접속 테스트를 성공한 것을 확인할 수 있다.

그러면 이번엔 렌더링된 페이지의 텍스트를 가져오는 테스트를 진행해보자.
새로 추가된 get Text 테스트는 해당 페이지의 Learn React란 문구를 contains 함수를 통해 찾으며 바로 클릭까지 해보는 테스트이다.

```js
describe('The Home Page', () => {
  beforeEach(() => {
    cy.exec('yarn start');
  });

  it('successfully loads', () => {
    cy.visit('/');
  });
  it('get Text', () => {
    cy.contains('Learn React').click();
  });
});
```

실행하게 되면 자동적으로 cypress가 Learn React 문구를 찾아 클릭까지 진행하게 되면서 리액트 공식 문서 페이지를 접속하게 되면서 테스트가 통과하게 된다.

![capture](/assets/images/posts/cypress4.png)

---

실제로 ui를 가시적으로 시나리오를 직접 작성하며 테스트할 수 있기에 더욱 E2E테스트가 중요해지는데, 여기서 cypress는 실제 브라우저 환경에서 테스트 할 수 있기에 더욱 큰 매력이 있다고 생각한다. 여기서는 간단한 조작에 대해서만 설명을 했는데 문서를 참고하면서 더 많은 기능에 대해 추후에 기록 해야겠다.

> cypress 공식문서 : <https://docs.cypress.io/guides/overview/why-cypress>
