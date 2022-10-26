---
layout: post
title: npm 라이브러리 배포에 package.json 최대한 활용하기 with cjs, esm, exports
description: cjs, esm을 모두 지원하는 라이브러리를 제작하고, npm 라이브러리 배포에 package.json exports를 활용해 sub path를 최적화하자. 그리고 exports를 지원하지 않는 webpack4 version 까지 대응하자.
categories: Web
tags: [Web, Frontend]
---

최근에 리액트 기반의 사내 디자인 시스템을 개발하였고, 직접 라이브러리로 패키징해서 npm에 배포했습니다. 그리고 라이브러리 내부에 추가적인 plugin까지 같이 묶어서 배포를 했습니다.

이 과정 속에서,

**es module** 파일과 **common js** 파일을 각각 지원하고, 위에서 언급한 plugin 모듈과 같은 **sub path 라이브러리**를 지원하기 위해 많은 시행착오를 겪었습니다. 이 경험에 대해서 pacakge.json의 option들을 기준으로 글을 작성해 보았습니다.

---

## 라이브러리 아키텍처와 files 옵션

라이브러리로 제작할 프로젝트 구조는 대략 아래와 같습니다.

- 📄 package.json
- 📄 tsconfig.json
- 📁 src
- 📄 rollup.config.js
- 📁 dist

패키지의 전반적인 내용을 관리하는 **package.json**과 타입스크립트 설정을 위한 **tsconfig.json**, 그리고 실제 라이브러리 소스코드(**src**)를 **rollup.config.js**를 통해 빌드를 하면 빌드되어 실제 결과물은 **dist** 폴더에 생성이 됩니다.

여기서 src를 제외한 dist폴더 내부의 파일만 라이브러리로 npm에 배포하는게 목적이므로 실제 package.json에서 참조할 파일들을 아래처럼 package.json 에서 명시했습니다.

```json
// package.json
{
  "files": ["dist"]
}
```

> files는 **node_modules에** 담길 파일들을 명시하는 옵션입니다.

---

## main, module, types 옵션과 cjs, esm

위의 방법까지 진행하고 그대로 배포한다면 실제로 라이브러를 쓰는 사람은 dist폴더까지 직접 참조해서 import를 해야하므로 많은 불편함이 있습니다.

이를 해결하기 위해 **main, module, types option**을 package.json에 추가하여 라이브러리에서 바로 dist폴더 내부의 index.js를 참조할 수 있습니다. 3가지 옵션은 각각 아래와 같은 의미를 가집니다.

- main: 라이브러리에서 root경로로 참조하게 될 default 경로
- module: es module import를 지원하는 환경에서 main 파일 대신 사용하게 될 module 경로
- types: typescript를 사용하는 환경에서 번들링 된 라이브러리의 타입을 지원하기 위한 d.ts 경로

위 3가지를 명시하기 전과 명시한 후를 비교하면 아래 예시와 같습니다.

### AS IS

파일을 import 할 때 dist 경로까지 명시해줘야 합니다.

```ts
import ex from 'my-library/dist';
```

### TO BE

package.json에 실제 파일 경로를 명시해준 뒤에 import경로를 간소화할 수 있습니다.

```json
// package.json
{
  "main": "./dist/index.js",
  "module": "./dist/esm/index.js",
  "types": "./dist/types/index.d.ts"
}
```

```ts
import ex from 'my-library';
```

> 여기서 번들링 결과물을 common js(cjs)와 es module(esm) 2가지 방식으로 각각 번들링 하고 main 옵션에는 cjs 파일 경로를 명시하고 module 옵션에는 esm 파일 경로를 명시해줘야 합니다. <br/>
일반적인 개발환경에서는 esm을 대부분 지원하겠지만, **next js**를 사용해서 **서버사이드 렌더링**까지 활용하는 경우는 cjs 형식을 부분적으로 필요로하는 경우가 있기 때문입니다. <br/>
(cjs가 아예 필요가 없는 경우에는 module옵션 대신에 main에 esm 파일 경로를 명시하고 type옵션에 module을 명시할 수 있습니다.)

---

## exports 옵션을 통한 sub path module 지원

만약 라이브러리 내부에 plugin module을 optional로 지원하고 싶다면 어떻게 해야할까요?

평소에 라이브러리들을 사용해보면 import는 /plugin만 추가함으로써 바로 적용하는 경험을 해보셨을 겁니다.

```ts
import plugin from 'my-library/plugin';
import ex from 'my-library';
```

이를 지원하기 위해 저는 아래와 같이 dist 폴더 내부에 번들 파일들을 구성했습니다.

- 📁 dist
  - 📄 index.js
  - 📁 esm
    - 📄 index.js
  - 📁 <u>**plugin**</u>
    - 📄 index.js
    - 📁 esm
      - 📄 index.js

하지만 실제로 plugin을 import 하려면 dist를 다시 참조해야 합니다. main option은 index.js 만 참조하는 것일 뿐 추가적인 js파일을 참조하기 위한 옵션은 아니기 때문입니다.

```ts
import plugin from 'my-library/dist/plugin';
```

이를 위해서 최근 package.json에는 **exports** 옵션이 추가됐습니다. exports 옵션을 활용하면 sub path로 라이브러리를 참조하기 쉽게 만들어주고, 위에서 언급했던 module 옵션처럼 sub module의 **esm 파일까지 추가로 지원**할 수 있습니다.

그럼 plugin 파일을 지원하기 위해 pacakge.json을 다시 설정해 보겠습니다.

```json
// package.json
{
  "exports": {
    "./plugin": {
      "types": "./dist/types/plugin/index.d.ts",
      "module": "./dist/plugin/esm/index.js",
      "default": "./dist/plugin/index.js"
    },
  },
}
```

라이브러리 바로 하위경로로 지정할 key를 "./plugin" 으로 명시해준 다음에, main 옵션 대신에 default옵션에 cjs 파일을 지정정는 것만 제외하곤 main의 index.js를 명시하는 과정과 비슷합니다.

이렇게 함으로써 plugin sub path를 바로 import 할 수 있습니다.

```ts
import plugin from 'my-library/plugin';
```

---

## exports 옵션을 지원하지 않는 webpack 4 환경에 대응하기

이렇게 package.json을 설정함으로써 sub path를 통한 plugin 지원까지 완료가 됐습니다. 하지만 **webpack 4**에서는 exports 옵션을 아직 지원하지 않아서 번들링 될 때 라이브러리들의 exports를 참조하지 못하는 이슈가 있습니다.

이를 해결하기 위해 exports를 지원하는 webpack5로 업그레이드 하면 되겠지만, 현재 대부분 **create-react-app**으로 만들어진 project들은 webpack4 버전을 사용중이라서 범용적인 라이브러리를 만드는것을 생각중이라면 이를 대응해야 합니다.

특히 저희 사내 프로젝트중 일부는 **craco**를 통해 cra 내부의 webpack 옵션을 오버라이딩 하고 있는데, craco library는 아직 webpack4 버전만을 지원하고 있어서 이를 마이그레이션 하는게 쉽지 않았습니다.

그래서 exports 옵션을 사용하되, exports를 지원하지 않는 환경도 호환하는 방식을 찾아보기 위해 여러 라이브러리를 뜯어본 결과, npm에 배포하는 패키지의 구성에는 **root경로를 dist폴더 내부로 재정의** 하여 배포하는 것으로 이해했습니다.

즉, 실제 라이브러리를 제작하는 프로젝트의 아키텍처는 아래처럼 구성하지만

- 📁 src
- 📄 rollup.config.js
- 📄 package.json
- 📄 tsconfig.json
- 📁 dist
  - 📄 index.js
  - 📁 esm
    - 📄 index.js
  - 📁 plugin
    - 📄 index.js
    - 📁 esm
      - 📄 index.js

**node_modules**에 들어갈 실제 패키지의 아키텍처는 아래처럼 구성됩니다.

- 📁 node_modules
  - 📁 my-library
    - 📄 package.json
    - 📄 index.js
    - 📁 esm
      - 📄 index.js
    - 📁 plugin
      - 📄 index.js
      - 📁 esm
        - 📄 index.js

둘의 차이점은 dist 내부의 파일들이 root경로에 바로 추가되며, package.json만 그대로 유지하는 것입니다.
이렇게 함으로써 webpack4 버전에서는 라이브러리 경로 뒤에 바로 exports 방식과 동일하게 **/plugin** 만 추가하여 라이브러리를 사용할 수 있습니다.

> **package.json**은 해당 패키지의 정보를 담고 있어서 배포할 때 root경로에 꼭 있어야 합니다.

> webpack4 버전에서는 esm 경로는 따로 인식하지 못하므로 cjs 파일을 참조하게 됩니다. esm만 사용하고 싶다면 /plugin/index.js 파일을 esm 형식으로 빌드하면 됩니다.

---

## package.json을 포함하여 dist 폴더 내부만 배포하기

그럼 dist폴더 내부를 root 경로로 지정하여 어떻게 배포할 수 있을까요?

가장 간단한 방법은 rollup을 통해 번들된 결과물을 dist가 아닌 root에 생성하고, **.npmignore** 파일에 결과물을 제외한 파일들(src, tsconfig.json, ...)을 명시해주면 됩니다. (github action을 통해 npm에 배포할 때 적합합니다)

하지만 저는 실제 프로젝트의 dist 폴더에 번들 파일들을 생성하는 것은 그대로 유지하고 싶었기에 아래와 같은 순서대로 진행하는 script를 만들었습니다.

1. rollup 번들을 실행하여 dist폴더에 결과물을 생성함. (npm run build)
2. root에 있는 package.json을 dist 폴더에 그대로 복사함. (cp pacakge.json dist)
3. 현재 작업중인 디렉토리 경로를 dist 내부로 이동해서 npm publish를 실행함. (cd dist && npm publish)

그럼 실제 package에서는 dist폴더가 사라졌으니 이에 맞게 저희가 작성한 package.json을 수정해 봅시다.

### AS IS

```json
// package.json
{
  "files": ["dist"],

  "main": "./dist/index.js",
  "module": "./dist/esm/index.js",
  "types": "./dist/types/index.d.ts",

  "exports": {
    "./plugin": {
      "types": "./dist/types/plugin/index.d.ts",
      "module": "./dist/plugin/esm/index.js",
      "default": "./dist/plugin/index.js"
    },
  },
}
```

### TO BE

```json
// package.json
{
  "files": ["**"],
  
  "main": "./index.js",
  "module": "./esm/index.js",
  "types": "./types/index.d.ts",

  "exports": {
    "./plugin": {
      "types": "./types/plugin/index.d.ts",
      "module": "./plugin/esm/index.js",
      "default": "./plugin/index.js"
    },
  },
}
```

기존의 dist 경로를 제거해주고, root 폴더에 번들링된 결과물들이 있으므로 **files** 옵션을 모든 파일을 포함할 수 있게 **\*\*로** 설정했습니다.

---

## 마치며

라이브러리를 만드는 과정을 되돌아보면, 내부 소스코드를 개발하는 과정보다 라이브러리를 사용하는 여러 환경에 모두 대응할 수 있게 세팅하고 최신의 기능들로 최적화하는 과정이 많은 버그로 인해 어려웠다고 느꼈습니다.

하지만 이러한 세팅 경험으로 자바스크립트 모듈 시스템과 번들러들에 대해서 깊게 알 수 있었습니다. 그리고 추가적인 라이브러리를 배포할 때, 같은 버그는 사전에 방지하여 많은 시간 단축을 할 수 있을거라 생각합니다.

마지막으로 가장 값진 경험이라 생각했던 것은, 라이브러리 배포과정에서 발생한 오류들을 해결하기 위해 이미 오픈소스로 잘 만들어진 라이브러리들을 뜯어보며 maintainer들이 왜 이렇게 작성했는지를 자연스럽게 알 수 있으므로 오픈소스 코드를 보다 쉽게 이해할 수 있는 발판이 된다는 것입니다.
