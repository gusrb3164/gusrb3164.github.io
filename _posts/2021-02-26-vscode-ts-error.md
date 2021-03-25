---
layout: post
title: 비주얼 스튜디오 코드 에서 타입스크립트 prettier 적용이 안될 때
description: vscode 에서 typescript prettier 자동 적용이 안될 때 해결방법
tags: [Programming, Tip]
---

### vscode에서 prettier 가 다운로드 돼있고, format on save 가 켜져있음에도 타입스크립트 파일에 prettier 가 적용이 안돼서 몇시간동안 찾아보았다. 알고보니 settings.json 에 타입스크립트 prettier연동을 따로 해줘야 한다고 한다.

---

### ctrl+shift+P 를 눌러서 Preferences: Open Settings (JSON) 을 클릭해 setting.json 으로 들어가자.

### 아래의 typescript-prettier 연동 코드를 넣어주면 된다. 리액트를 사용하지 않는 경우에는 위의 것만 적용시켜도 된다.

```json
"[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
"[typescriptreact]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
```

해당코드를 넣은뒤에 prettier 관련 기능들이 기본 포맷 혹은 .prettierrc 의 설정을 통해 다시 인식하기 시작한다.
