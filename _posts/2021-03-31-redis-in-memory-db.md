---
layout: post
title: In memory db인 Redis 특징
description: In memory db인 Redis의 특징
categories: Computer-Science
tags: [CS, DB]
---

## Redis란?

> 오픈소스인 인 메모리 키 값 데이터 구조 스토어이다(NOSQL + In memory). 데이터 처리가 매우 빠르다.

## 특징

1. RAM에 데이터를 직접 저장해서 디스크 스캐닝이 필요없는 빠른 데이터 처리가 가능하다.
2. 캐싱도 가능해 실시간 채팅에 적합하며 세션 공유를 위해 세션 클러스터링에도 활용된다.

## 백업 과정

RAM은 휘발성 메모리이기 때문에 예기치 못한 오류가 발생해 종료되면 데이터가 다 날아가게 된다. 이러한 문제를 해결하기 위해 Redis의 백업 해결책이 있다.

-   snapshot : 특정 지점을 설정하고 디스크에 백업해 놓는다
-   AOF(Append Only File) : 이전에 사용한 명령어 들을 파일에 저장해놓고, 데이터가 사라질경우 해당 파일의 로그를 읽어서 데이터를 복원한다

## 데이터 구조

레디스는 key/value 형식으로 데이터가 이루어져 있으며 value에는 5가지 타입이 존재한다.

1. String (text, binary data) - 512MB 까지 저장이 가능하다
2. Set (String 집합)
3. Sorted Set (Set을 정렬해둔 상태)
4. Hash
5. List (양방향 연결리스트도 가능)
