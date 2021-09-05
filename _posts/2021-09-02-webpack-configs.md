---
layout: post
title: 웹팩에 들어가는 속성에 대해서
description: 웹팩 설정을 구성하는 4가지의 속성 entry, output, loader, plugin에 대해서
categories: Web
tags: [Programming, Web, Frontend]
---

## 웹팩의 4가지 주요 속성

1. entry
2. output
3. loader
4. plugin

### entry

웹팩에서 웹 자원을 변환하기 위해 필요한 최초 진입점이자 자바스크립트 파일 경로

EX) 한번에 모든 파일을 보여주는 싱글 페이지 애플리케이션의 경우는 모든 파일들을 import하는 index.js와 같은 파일을 entry에 넣어준다.

```js
module.exports = {
  entry: './src/index.js',
};
```

EX) 페이지마다 새로 module을 가져오는 경우에는 entry에 키와 값을 매칭해준다.

```js
module.exports = {
  entry: { login: './src/LoginView.js', main: './src/MainView.ks' },
};
```

### output

웹팩을 돌리고 난 결과물의 파일 경로

EX) filename은 필수로 지정해줘야 하며 일반적으로 path 속성(해당 파일의 경로)을 함께 정의한다.

```js
const path = require('path');

module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
  },
};
// 해당 결과물은 ./dist/bundle.js 에 남게된다.
```

#### output 파일 이름에는 여러 옵션을 넣을 수 있다

- 결과 파일 이름에 entry 속성을 포함하는 옵션

```js
module.exports = {
  output: {
    filename: '[name].bundle.js',
  },
};
```

- 결과 파일 이름에 웹팩 내부적으로 사용하는 모듈 ID를 포함하는 옵션

```js
module.exports = {
  output: {
    filename: '[id].bundle.js',
  },
};
```

- 매 빌드시 마다 고유 해시 값을 붙이는 옵션

```js
module.exports = {
  output: {
    filename: '[name].[hash].bundle.js',
  },
};
```

- 웹팩의 각 모듈 내용을 기준으로 생성된 해시 값을 붙이는 옵션

```js
module.exports = {
  output: {
    filename: '[chunkhash].bundle.js',
  },
};
```

### loader

웹팩이 웹 애플리케이션을 해석할 때 자바스크립트 파일이 아닌 HTML, CSS, Images, Font 등을 변환할 수 있도록 도와주는 속성

module 이라는 속성을 이용한다.

EX)

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['css-loader'],
      },
    ],
  },
};
```

- test 는 로더를 적용할 파일 유형을 의미하며 일반적으로 정규 표현식으로 처리한다.
- use 는 해당 파일에 적용할 로더의 이름이다.

#### 주로 사용되는 로더의 종류

1. css-loader
2. style-loader
3. file-loader
4. url-loader
5. babel-loader
6. sass-loader
7. vue-loader
8. ts-loader

#### 로더 적용 순서

특정 파일에 대해 여러 개의 로더를 사용하는 경우 오른쪽에서 왼쪽 순으로 적용되는 순서를 유의해야 한다.

EX) css 확장 문법인 scss 파일의 로더는 scss-loader를 먼저 적용해야 한다.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['css-loader', 'sass-loader'],
      },
    ],
  },
};
```

### plugin

웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성. 로더는 파일을 해석하고 변환하는 과정에 관여하지만, 플러그인은 해당 결과물의 형태를 바꾸는 역할을 한다.

플러그인은 배열 형식으로, 배열 안에는 생성자 함수로 생성한 객체 인스턴스만 추가될 수 있다.

EX)

```js
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [new HtmlWebpackPlugin(), new webpack.ProgressPlugin()],
};
/*
 HtmlWebpackPlugin : 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
 ProgessPlugin : 웹팩의 빌드 진행율을 표시해주는 플러그인
 */
```
