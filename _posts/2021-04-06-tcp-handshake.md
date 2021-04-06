---
layout: post
title: TCP의 3 way handshake와 4 way handshake
description: 3 way handshake와 4 way handshake 의 방식, 서로 다른 이유
categories: Computer-Science
tags: [CS, Network]
---

## TCP란

-   TCP는 장치들 사이에 논리적인 접속을 성립하기 위하여 연결을 설정하여 신뢰성을 보장하는 연결형 서비스이다.

---

## 3-way handshake 란

-   TCP 통신을 이용하여 데이터를 전송하기 위해 네트워크 연결을 설정 하는 과정
-   양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하며, 실제로 데이터 전달이 시작되기 전에 한 쪽이 다른 쪽이 준비되었다는 것을 알 수 있도록 한다.
-   즉, TCP/IP 프로토콜을 이용해서 통신을 하는 응용 프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방측과 사전에 세션을 수립하는 과정을 의미한다.

![3way-handshake](https://raw.githubusercontent.com/WeareSoft/tech-interview/master/contents/images/3-way-handshaking.png)

---

## 4-way handshake 란

-   TCP의 연결을 해제하는 과정이다
-   TCP 연결하는 경우보다 1단계 더 있는 이유
    -   연결을 끊을 주체가 데이터 전송을 마쳤다고 하더라도 서버측에서 아직 보낼 데이터가 남아있을 수 있기 때문에 일단 FIN에 대한 ACK만 보내고, 데이터를 모두 전송한 후에 자신도 FIN 메시지를 보내기 때문이다

![4way-handshake](https://raw.githubusercontent.com/WeareSoft/tech-interview/master/contents/images/4-way-handshaking.png)

### 4-way handshake 순서

1. A -> B : FIN
    - A가 연결을 종료하겠다고 FIN 플래그를 전송한다
2. B -> A : ACK
    - B는 일단 확인 문자를 보낸다
    - 다음 마저 보내던 데이터를 전송을 하고 A는 수신완료 ACK를 보낸다 (그림에서 3번째 화살표, 데이터가 없다면 바로 3번 진행)
3. B -> A : FIN
    - B가 통신이 끝났으면 A에게 FIN 플래그를 전송한다
4. A -> B : ACK
    - A는 확인 메시지를 전송하고 서로 연결을 종료한다
