---
layout: post
title: 소켓연결, 비연결 때 데이터 주고받기
description: 자바스크립트 socket.io 를 통한 connect, disconnect 발생 시에도 데이터를 주고 받는 방법
categories: Web
tags: [Programming, javascript]
---

react와 nest js 로 채팅 서비스를 구현하고 있는데 connect, disconnect 이벤트가 발생할 때 어떤 사용자가 접속하고 나가는지 확인할 수단이 필요했다.

기본적으로 연결과 비연결 때 제공하는 정보는 클라이언트의 socket id만 있기 때문이다.

그래서 처음 구현했던 방식은 join 과 leave 라는 데이터 전달용 이벤트를 각각 추가햐서 클라이언트가 connect 하면 join 이벤트도 추가로 발생시키고 disconnect 하기전에 leave 이벤트를 발생시키도록 했다.

하지만 이렇게 하면 **2가지 문제점**이 존재했다.

1. connect와 disconnect가 기본적으로 존재하는데 이러한 역할을 하는 이벤트를 또 추가함으로써 불필요한 이벤트가 생기고, connect가 발생하고 join을 추가로 기다려야 사용자를 확인할 수 있는 버퍼가 추가된다.

2. 가장 큰 문제점은 사용자가 채팅에서 벗어나는 걸 인지하면 (ex: useEffect의 cleanup 함수) 클라이언트측 브라우저에서 leave 이벤트를 emit 해야하는데, 브라우저를 강제 종료 해버리면 disconnect만 발생하고 leave이벤트는 emit을 못하게 된다.

---

그래서 찾아보니 소켓객체에 socket.handshake.query와 socket.data 라는 속성이 있어서 이를 활용하면 connect, disconnect에도 클라이언트의 정보를 확인할 수가 있다.

## Socket Query

---

클라이언트가 서버에게 소켓 연결을 시도할 때 url에 query를 추가할 수 있다. query를 추가하여 연결을 하면 서버측에서는 받은 소켓 객체의 handshake.query 에 해당 query들이 담기게 된다.

EX) 클라이언트에서 연결할 때 query 전달

```ts
const socket = io.connect(REACT_APP_ROOT, { path: '/chat', query: { userId: 5 } });
```

EX) 서버에서 query 접근 (데이터는 string 형식으로 온다.)

```ts
handleConnection(@ConnectedSocket() socket: Socket) {
    const { userId } = socket.handshake.query
    console.log(userId) // '5'
}
```

이런 식으로 사용하면 사용자가 누구인지 확인하는 userId를 connection 이벤트에서 바로 확인할 수 있다.

## Socket Data

---

query를 통해 사용자의 정보를 받았다면 disconnect할 때는 서버측에서 이 정보를 유지하고 있어야 어떤 사용자가 disconnect 하는지 알 수 있을 것이다.
이를 위해 각각 연결 된 socket 객체의 data라는 속성에 사용자의 정보를 저장해두면 disconnect할 때 data가 저장된 socket 객체를 전달받을 수 있다.

EX) connect 함수에서 받은 query를 data에 저장해두고 disconnect에서 사용한다

```ts
handleConnection(@ConnectedSocket() socket: Socket) {
    const { userId } = socket.handshake.query
    socket.data = { userId }
}

handleDisconnect(@ConnectedSocket() socket: Socket) {
    const { userId } = socket.data
}
```

> client에서 server로 연결할 때도 data 옵션이 있다면 좋겠지만 없어서 query에 추가해서 보내야한다.

---

위의 방법을 몰랐다면 join, leave 이벤트를 유지한 채로 redis에 socket Id와 사용자 정보를 각각 맵핑시켜놓고 disconnect가 발생할 때마다 redis에 접근해서 확인하려 했는데 이제라도 알아서 다행이다.
