---
layout: post
title: Restful API 란
description: restful api 의 정의와 이를 지키는 디자인 원칙
categories: Web
tags: [Programming, Backend, CS, Web]
---

# RESTful API

> 월드 와이드 웹과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식으로 자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반에 대한 패턴

REST란 API 설계의 중심에 <b>자원</b>이 있고 <b>HTTP Method</b>를 통해 자원을 처리하도록 설계하는 것이다. 즉, REST의 기본 원칙을 성실히 지킨 서비스 디자인을 RESTful하다라고 표현한다.

# RESTful하게 API를 디자인 하는법

1. 리소스와 행위를 명시적이고 직관적으로 분리한다

    - 리소스는 <b>URI</b>로 표현되는데 리소스가 가리키는 것은 <b>명사</b>로 표현되어야 한다.
    - 행위는 HTTP Method로 표현하고, GET, POST, PUT, PATCH, DELETE 메서드가 존재한다.
        > URI (Uniform Resource Identifier) : 네트워크 상에 존재하는 자원을 구분하는 식별자(ID)로서 의미

2. Message는 Header와 Body를 명확하게 분리해서 사용한다

    - Entity에 대한 내용은 Body에 담는다.
    - 애플리케이션 서버가 행동할 판단의 근거가 되는 컨트롤 정보인 API 버전 정보, 응답받고자 하는 MIME타입 등은 header에 담는다.

3. API 버전을 관리한다

    - 환경은 항상 변하기 때문에 API의 signature가 변경될수도 있기 때문이다.
    - 특정 API를 변경할 때는 반드시 하위호환성을 보장해야 한다.

4. 서버와 클라이언트가 같은 방식을 사용해서 요청하도록 한다
    - 브라우저와 서버는 form-data 형식 혹은 json 형식중 하나로 통일해야 한다.
    - 즉, URI가 플랫폼 중립적이어야 한다.

# RESTful API의 장점

1. 형식이 정해져 있어 OPEN API를 제공하기 쉽다.
2. 원하는 타입으로 데이터를 주고 받을 수 있다.
3. 기존 웹 인프라(HTTP)를 그대로 사용할 수 있다.

# RESTful API의 단점

1. 사용할 수 있는 메소드가 한정돼 있다.
2. 분산환경에는 부적합하다.
3. HTTP통신 모델만 지원한다.
