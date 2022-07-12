---
layout: post
title: 프론트엔드 개발자로서 요즘의 고민
description: 컴포넌트 설계, hooks and utils, 비동기 데이터 관리, 프로젝트 구조 관리
categories: Web
tags: [Programming, Frontend]
---

요즘 프론트엔드 관련 개발을 하면서 느끼는 고민들을 기록해봤습니다.

이런 고민들의 정답을 위해 계속 고민하면서 느끼는 것은 결국 코드의 재사용성, 유지보수성, 성능을 모두 잡기 위해 끊임없이 생각해야 하는 과제라는 것입니다.

## 컴포넌트 설계

1. 컴포넌트간의 상태관리를 상위 컴포넌트로 일부 위임하는게 적절한 경우?
2. 특정 상태를 기반으로 동작하는 컴포넌트를 설계함에 있어서 해당 상태를 props로 받아서 관리할 것인지 local state로 관리하여 callback으로 전달할 것인지에 있어서의 기준?

## hooks and utils

1. hook과 util을 명확하게 구분지을 수 있을까?
2. 상태에 관여하지 않는 함수는 hook으로 관리할 필요가 전혀 없는가?

## 비동기 데이터 관리

1. 데이터 유형, 데이터 캐싱의 시간에 따라서 성능, UX, 데이터 정확성을 잘 고려하는 법?
2. 프론트엔드에서는 데이터베이스와 어느정도까지 동기화 되어야 할까?
3. 타입스크립트를 통한 비동기 인터페이스 관리는 전역적으로? 아니면 해당 상태를 다루는 로직과 같은 위치에서?
4. 현재 적용하는 비동기 타입 관리는 전역적인 models 폴더애서 graphql과 유사하게 기본적인 entity들을 구성하는 타입들을 선언하고, api response는 이들을 이용하여 파생되는 인터페이들을 작성하는 방식으로 구성하고 있다. 보다 좋은 방법은 무엇이 있을까?

## 프로잭트 구조 관리

1. 공통적으로 쓰이는 common을 어느정도까지 허용해야 할까?
2. 공통적으로 쓰이는 기능들을 각각 파일로? 최소한의 동작을 공통적으로 공유하는 파일별로?
3. 당장 공통으로 사용되는 로직은 아니지만 추후 가능성이 있다면 미리 분리하는 것이 옳은가?