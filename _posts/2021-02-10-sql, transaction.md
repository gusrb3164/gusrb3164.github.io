---
layout: post
title: sql의 종류와 트랜잭션의 특성
description: sql의 종류와 트랜잭션의 정의와 특성
categories: Computer-Science
tags: [CS, DB]
---

## sql 이란?

sql은 관계형 데이터베이스에 대해서 데이터의 구조를 정의, 데이터 조작, 데이터 제어 등을 할 수 있는 절차형 언어이다.

## sql 종류

-   DDL (Data Definition Language)

    -   관계형 데이터베이스의 구조를 정의하는 언어
    -   CREATE, ALTER, DROP, RENAME 문이 있다.

-   DML (Data Manipulation Language)

    -   테이블에서 데이터를 입력, 수정, 삭제, 조회한다
    -   INSERT, UPDATE, DELETE, SELECT 문이 있다.

-   DCL (Data Control Language)

    -   데이터베이스 사용자에게 권한을 부여하거나 회수한다
    -   GRANT, REVOKE, TRUMCATE 문이 있다.

-   TCL (Transaction Control Language)

    -   트랜잭션을 제어하는 명령어이다.
    -   COMMIT, ROLLBACK, SAVEPOINT 문이 있다.

## 트랜잭션이란?

트랜잭션은 데이터베이스의 작업을 처리하는 단위이다.

## 트랜잭션의 특성

-   원자성 (Atomicity)

    -   트랜잭션은 데이터베이스 연산의 전부가 실행되거나 전혀 실행되지 않아야 한다. (ALL OR NOTHING)
    -   즉, 트랜잭션의 처리가 완전히 끝나지 않았을 경우는 실행되지 않은 상태와 같아야 한다.

-   일관성 (Consistency)

    -   트랜잭션 실행 결과로 데이터베이스의 상태가 모순되지 않아야 한다.
    -   트랜잭션 실행 후에도 일관성이 유지되어야 한다.

-   고립성 (Isolation)

    -   트랜잭션 실행 중에 생성하는 연산의 중간결과는 다른 트랜잭션이 접근할 수 없다.
    -   즉, 부분적인 실행 결과를 다른 트랜잭션이 볼 수 없다.

-   영속성 (Durability)
    -   트랜잭션이 그 실행을 성공적으로 완료하면 그 결과는 영구히 보장이 되어야 한다.
