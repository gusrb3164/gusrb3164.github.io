---
layout: post
title: axios interceptors와 refresh token을 활용한 jwt 토큰 관리
description: axios 요청에 기반하는 frontend 구조에서 axios interceptors를 활용하여 access token과 refresh token을 관리해보자
categories: Web
tags: [Programming, Frontend]
---

새로운 도메인을 개발하면서 jwt를 활용한 인가 처리를 적용하기 위해 frontend와 backend 구현을 모두 맏았습니다. 이 과정에서 access token과 refresh token을 관리하기 위해 어떤 과정을 고려했는지, axios를 어떻게 활용하였는지 기록한 글입니다.

## 1. [JWT](https://jwt.io/)

![jwt logo](/assets/images/posts/jwt.png)

jwt와 같은 bearer 토큰은 기본적인 access token만으로도 서버에서 secret key를 통한 서명으로 인해 토큰 위변조 여부를 확인하여 사용자 인가를 처리할 수 있습니다. 하지만 **탈취 가능성**을 고려하여 대부분 access token은 만료 시간을 짧게 설정하여 탈취 후 악용의 위험성을 최소화 합니다.

이 과정에서 정상적인 사용자는 짧은 만료 시간으로 인해 잦은 access token 재발급의 불편함을 겪어야 합니다. 그래서 이 불편함을 해소하고, access token의 **stateless한 특징에서 발생하는 보안 취약점**을 보완하고자 refresh token을 활용합니다.

refresh token은 상대적으로 access token보다 긴 만료시간을 가지고 있기 때문에 유저가 access token이 만료됐다면 refresh token을 기반으로 access token을 갱신할 수 있습니다.

그리고 refresh token은 access token과 달리 db혹은 redis와 같은 스토리지에 저장되어 만료기간과 상관없이 refresh token이 최신 상태인지 확인할 수 있는 특징도 있습니다.

---

## 2. refresh token을 활용하여 access token을 갱신하는 과정

access token과 refresh token을 다루기 위해선 **access token을 갱신하는 과정**과 **refresh token을 갱신하는 과정** 2가지를 고려해야 합니다.

우선 **만료된 access token을 갱신하는 방식**은 3가지로 분류했습니다.

### 만료된 access token을 갱신하는 방식

1. **client**에서 access token 만료 가능성을 확인 후, 만료됐거나 만료가 임박하면 api request header에 추가적인 refresh token을 실어서 보낸다. 요청을 받은 **server**는 header에 유효한 refresh token이 존재한다면 해당 api까지는 정상적인 요청으로 간주하고 갱신된 access token을 추가하여 응답으로 보내는 방식.
    - 추가적인 refresh token 갱신 요청을 보내지 않아도 되지만, **server**는 api 요청마다 refresh token 여부를 확인하고 존재한다면 갱신된 access token을 응답에 추가적으로 보내야합니다. **client**는 api 요청마다 access token을 decoding 한 후에 만료 시간을 체크하여 기존 요청에 refresh token을 추가하는 로직과 응답에 access token이 존재하는지 체크하는 로직을 추가해야 합니다.

    ![도식화 이미지](/assets/images/posts/2022-08-07/1.png)

2. **client에**서 access token 만료 가능성을 확인 후, 만료됐거나 만료가 임박하면 api 요청을 보내기 전에 먼저 refresh 요청을 먼저 보낸 다음 갱신된 access token을 가지고 본 요청을 보내는 방식.
    - 추가적인 refresh token 갱신 요청을 사전에 보내야 하지만, **server**는 독립적인 refersh token 갱신 api 요청만 제공하면 더이상 신경쓰지 않아도 됩니다. 하지만 **client**에서는 요청을 보내기 전마다 access token을 decoding 한 후에 만료 시간을 체크하여 요청을 먼저 보내는 로직을 추가해야 합니다.

    ![도식화 이미지](/assets/images/posts/2022-08-07/2.png)

3. **client**에서 api 요청을 보낸 후에 **server**가 만료 여부를 파악한 뒤에 401 response를 보내고, 응답을 받은 **client**가 refresh api 요청을 보내 갱신된 access token으로 재 요청을 보내는 방식.
    - 추가적인 refresh token 갱신 요청과 api 재요청을 보내야 하지만, **server**는 독립적인 refresh token 갱신 api만 제공하면 더이상 신경쓰지 않아도 됩니다. 하지만 **client**에서는 401요청 후에 refresh api 요청을 보내고 갱신된 access token으로 응답을 재요청하는 로직을 추가해야 합니다.

    ![도식화 이미지](/assets/images/posts/2022-08-07/3.png)

위의 3가지 방식중에 3번 채택했습니다. 해당 방식은 api 요청이 많아질 순 있으나 api가 refresh에 의존적이지 않고 독립적으로 작성할 수 있으며, client의 경우는 구현할 수 있는 best practice들이 많았기 때문에 관리측면에서도 편하다고 생각했습니다.

### refresh token의 갱신 여부

refresh token을 갱신하는 방식은 2가지로 분류했습니다.

1. refresh token은 갱신하지 않고 access token만 갱신하는 방식.
    - 정해진 만료 기간이 지나면 그대로 재 로그인을 강제시키게 됨.

    ![도식화 이미지](/assets/images/posts/2022-08-07/4.png)

2. server에서 refresh 요청을 받았을 때 받은 refresh token의 만료 기간이 임박하면 access token의 갱신과 더불어 refresh token도 갱신하는 방식.
    - refresh token이 만료되지 않는 한 사용자는 로그인 과정을 거치지 않고 지속적으로 access token과 refresh token을 갱신받게 됨.

    ![도식화 이미지](/assets/images/posts/2022-08-07/5.png)

위의 2가지 방식중에 2번을 채택했습니다. refresh token도 주기적인 갱신을 거쳐서 지속적으로 이용하는 사용자에게 로그인의 불편함을 최소한으로 하기 위함입니다.

그러면 선택했던 2가지 방식을 종합하여 access token과 refresh token을 다루는 시나리오를 DB까지 고려하여 최종적으로 확인해봅시다. **server**는 request api에 대해서만 신경쓰고, **client**는 request와 response를 처리하는 과정만 신경써주면 그 외에 복잡한 로직은 없어보입니다.

![도식화 이미지](/assets/images/posts/2022-08-07/6.png)

---

## 3. [axios interceptors](https://axios-http.com/kr/docs/interceptors)

axios interceptors 기능을 활용하면 response와 request를 가로채어 추가적인 작업을 할 수 있습니다.

1. 요청을 가로채면 사전에 정의한 callback함수를 먼저 실행한 결과와 함께 요청을 보냅니다.
2. 응답을 가로채면 받은 응답을 기반으로 사전에 정의한 callback함수를 먼저 실행하여 최종 응답을 받도록 설정할 수 있습니다.

그러면 response 와 request 요청마다 axios interceptors를 활용하여 저희가 요청 전, 후에 다뤄야 할 callback 함수들을 붙여봅시다.

### (1) axios interceptors request

우선 client localStorage에 저장된 jwt 토큰을 요청마다 Header의 Authorization에 담아서 보낸다고 가정하고 axios interceptors request를 먼저 정의해 보겠습니다.

```ts
axios.interceptors.request.use((config) => {
  if (!config.headers) return config;

  let token: string | null = null;

  if (config.url === REFRESH_URL) {
    token = localStorage.getItem('refreshToken');
  } else {
    token = localStorage.getItem('accessToken');
  }

  if(token !== null){
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

refresh 요청을 제외한 요청들은 access token을 authorization에 담아야 하므로 해당 조건을 분기해주어 알맞는 토큰을 localStorage에서 가져와 Bearer 형식으로 config에 저장해주면 되는 간단한 로직입니다.

### (2) axios interceptors response

다음은 client가 401 에러를 받았을 때 refresh 요청을 보낸 뒤에 이전에 보냈던 요청을 재요청 하는 callback함수를 axios interceptors response에 정의해 보겠습니다.

```ts
const getRefreshToken = async (): Promise<string | void> => {
  try {
    const { data: { accessToken, refreshToken } } = await axios.get<{ accessToken: string; refreshToken: string | null }>(REFRESH_URL);

    localStorage.setItem('accessToken', accessToken);

    if (refreshToken !== null) {
      localStorage.setItem('refreshToken', refreshToken);
    }

    return accessToken;
  } catch (e) {
    localStorage.removeItem('accessToken');
    localStorage.removeItem('refreshToken');
  }
}


axios.interceptors.response.use(
  (res) => res,
  async (err) => {
    const { config, response: { status } } = err;

    /** 1 */
    if (config.url === REFRESH_URL || status !== 401 || config.sent) {
        return Promise.reject(err);
    }

    /** 2 */
    config.sent = true;
    const accessToken = await getRefreshToken();

    if (accessToken) {
      config.headers.Authorization = `Bearer ${accessToken}`;
    }

    return axios(config);
  }
);
```

response callback 함수는 정상적인 응답을 첫 번째 인자로, 에러 응답을 두 번째 인자로 전달받습니다. 저희는 401 에러의 경우를 처리해야 하므로 두 번째 인자에 대해서만 callback 함수를 정의하면 됩니다.

우선 refresh 요청자체의 에러나 401 에러가 아닌경우는 refresh를 해야할 필요가 없으므로 에러를 그대로 reject해줍시다.

그리고 refresh 요청이 끝나고 재요청을 보냈는데도 에러가 발생한 경우 재귀적으로 loop가 발생할 수 있기 때문에 이를 방지하기 위한 주석 2번 부분처럼 config.sent를 true로 설정해주고 주석 1번 절의 조건에 함께 추가해줍니다.

이제 위의 주석 1번 if문에 걸러지지 않는 에러들은 refresh 요청을 보내야 합니다. 따라서 getRefreshToken이라는 요청을 통해 새로운 access token을 갱신받고, 401 에러가 발생했던 기존의 요청을 새로운 access token을 통해 재요청을 보내면 실제 client는 굳이 access token을 갱신하는 과정을 직접 하지 않아도 되는 번거로운 과정을 생략할 수 있습니다.

> getRefreshToken의 요청에 대한 try catch문도 필수로 작성되어야 refresh api가 에러나는 경우를 대비할 수 있습니다. 저는 catch문에서 사용자의 localStorage에 저장된 token들을 지웠습니다.

---

## 4. refresh 중복 요청 문제

이로써 제가 생각했던 시나리오를 axios interceptors를 활용하여 모두 작성했지만 여기서 한가지 문제점이 존재합니다.

바로 client 측에서 만료된 access token으로 다수의 요청을 동시에 server에 보내서 각 요청마다 refresh 요청을 독립적으로 보내는 경우입니다.

이게 왜 문제가 되는걸까요?

예를 들어 아래의 방식처럼 access token을 필요로 하는 2개의 api 요청을 거의 동시에 보내고 이후에 refresh 
요청을 각각의 interceptor에서 보냈을 때, refresh token 또한 새로 갱신해 줘야 하는 상황이라고 가정해봅시다.

![중복 요청 예제](/assets/images/posts/2022-08-07/7.png)

그러면 server 측에서는 각각의 요청에 대해 access token을 새로 sign 함과 동시에 refresh token도 2개를 sign하게 됩니다.

여기서 access token은 **stateless**하기 때문에 어떤 token을 client에서 저장하든 문제가 되지 않지만, refresh token은 DB에서 1개만 관리하기 때문에 DB에 최종적으로 override된 refresh token을 client에게 마지막 response로 보내줘야 합니다.

하지만 client측에서 response를 항상 순차적으로 받는다는 보장은 없기에 DB에 저장된 refresh token과 다른 refresh token이 최종적으로 client localStorage에 저장될 수 있습니다.

![중복 요청 예제](/assets/images/posts/2022-08-07/8.png)

따라서 이러한 중복 요청 문제는 access token과 refresh token을 불필요하게 여러번 sign하는 문제점과, DB에 저장된 refresh token과 다른 token을 client가 지니게 될 수 있다는 문제점이 존재합니다.

---

## 5. refresh 중복 요청 해결하기

이러한 중복 요청을 해결하기 위해선 client 측에서 여러번 가는 refresh 요청들을 한번의 refresh 요청만 가도록 처리해야 합니다. 요청을 한번만 가도록 처리하기 위해 조사한 방식은 2가지입니다.

1. race condition and subscription
2. memoization refresh api

### (1) race condition and subscription

1. 가장 먼저 요청한 refresh api를 다룰 때 lock 변수를 이용하여 그 뒤에 이어지는 refresh 요청들은 요청을 보내지 않도록 합니다.
2. 그 뒤의 요청들은 구독리스트에서 대기하고 있습니다.
3. 처음 요청의 응답이 옴과 동시에 구독리스트에 담긴 대기 api들을 호출합니다.
4. 호출된 구독 api들은 첫 요청으로 갱신된 access token과 refresh token을 가지고 기존 api를 재요청 보냅니다.

```ts
let lock = false;
let subscribers: ((token: string) => void)[] = [];

function subscribeTokenRefresh(cb: (token: string) => void) {
  subscribers.push(cb);
}

function onRrefreshed(token: string) {
  subscribers.forEach((cb) => cb(token));
}

const getRefreshToken = async (): Promise<string | void> => {
  try {
    const { data: { accessToken, refreshToken } } = await axios.get<{ accessToken: string; refreshToken: string | null }>(REFRESH_URL);

    lock = false;
    onRrefreshed(accessToken);
    subscribers = [];
    localStorage.setItem('accessToken', accessToken);

    if (refreshToken !== null) {
      localStorage.setItem('refreshToken', refreshToken);
    }

    return accessToken;
  } catch (e) {
    lock = false;
    subscribers = [];
    localStorage.removeItem('accessToken');
    localStorage.removeItem('refreshToken');
  }
}

axios.interceptors.response.use(
  (res) => res,
  async (err) => {
    const {
      config,
      response: { status },
    } = err;
    const originalRequest = config;

    if (config.url === REFRESH_URL || status !== 401) return Promise.reject(err);

    if (lock) {
      return new Promise((resolve) => {
        subscribeTokenRefresh((token: string) => {
          originalRequest.headers.Authorization = `Bearer ${token}`;
          resolve(axiosV2(originalRequest));
        });
      });
    }
    lock = true;
    const accessToken = await getRefreshToken()

    if (typeof accessToken === 'string') {
      config.headers.Authorization = `Bearer ${accessToken}`;
      return axios(config);
    }

    return  Promise.reject(err);
  }
);
```

### (2) memoization refresh api

1. 처음 요청된 refresh api를 일정시간 memoization 합니다.
2. memoization된 시간동안 추가적으로 요청되는 refresh 요청들은 재사용됩니다.
3. refresh 응답이 오면은 memoization된 api를 기다리던 요청들은 동일한 access token과 refresh token으로 기존 api를 재요청 보낼 수 있습니다.

```ts
import mem from 'mem';

const getRefreshToken = mem(async (): Promise<string | void> => {
  try {
    const { data: { accessToken, refreshToken } } = await axios.get<{ accessToken: string; refreshToken: string | null }>(REFRESH_URL);

    localStorage.setItem('accessToken', accessToken);

    if (refreshToken !== null) {
      localStorage.setItem('refreshToken', refreshToken);
    }

    return accessToken;
  } catch (e) {
    localStorage.removeItem('accessToken');
    localStorage.removeItem('refreshToken');
  }
}, { maxAge: 1000 })


axios.interceptors.response.use(
  (res) => res,
  async (err) => {
    const { config, response: { status } } = err;

    if (config.url === REFRESH_URL || status !== 401 || config.sent) {
      return Promise.reject(err);
    }

    config.sent = true;
    const accessToken = await getRefreshToken();

    if (accessToken) {
      config.headers.Authorization = `Bearer ${accessToken}`;
      return axios(config);
    }

    return Promise.reject(err);
  }
);
```

2가지 방식 모두 같은 결과를 이뤄낼 수 있으나 첫 번째의 구독 방식은 함수 외부에서 전역 변수에 의존해서 구현해야 하기 때문에 코드의 복잡도가 보다 높다고 생각했습니다.

따라서 기존의 callback 함수를 최대한 건드리지 않으면서 보다 직관적인 코드를 작성할 수 있는 memoization 방식을 선택했습니다.

> 위의 예제에서는 memoization을 이용하기 위해 mem library를 사용했습니다.
---

## 마치며

jwt는 확장성이 높은 인가 처리 방식이면서 refresh token을 활용한다면 보안을 보다 강화할 수 있습니다.

다만 이러한 token들을 어디에 저장하고 어떻게 전달하면서 갱신하고, 다양한 기기에서 다중 로그인을 지원하기 위해 여러개를 어떻게 다룰 것인지에 따라 구현 난이도가 기하급수적으로 올라가며 best practice로 다뤄지는 문서를 찾는것도 쉽지 않습니다. 그래서 제가 선택한 방식으로 당장 프로젝트에 적용했지만 더 개선할 수 있는 방식을 찾는다면 지속적으로 업데이트 해볼 생각입니다.

이 글이 인가처리를 구현하려는 분들에게 많은 도움이 되길 바랍니다.

### 참고문서

- memoization 기법 <https://gist.github.com/ModPhoenix/f1070f1696faeae52edf6ee616d0c1eb>

- subscription 기법 <https://dev.to/franciscomendes10866/how-to-use-axios-interceptors-b7d>
