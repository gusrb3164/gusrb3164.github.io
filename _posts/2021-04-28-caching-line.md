---
layout: post
title: 캐싱라인(Caching line)의 종류
description: 캐시메모리를 효율적으로 사용하기 위한 캐싱 라인의 종류를 이해하기
categories: Computer-Science
tags: [OS, CS]
---

> 캐시 메모리에 대한 이전 게시글 <https://gusrb3164.github.io/computer-science/2021/04/26/cache-memory/>

# 캐싱라인

보통 주소에 따라서 캐시메모리에 값을 저장하면 특정 캐시메모리에 해당 메모리 값이 있는지 바로 탐색이 가능하므로 주소별로 정해진 캐시메모리의 위치가 존재한다.(해시 테이블과 비슷한 원리이다)

하지만 캐시메모리의 크기는 메인 메모리보다 훨씬 작기 때문에 매핑하기위해선 주소값 전체를 사용하지 않고 특정 값만 인덱스로 사용하고 다른 값은 태그(Tag)로 사용하게 된다. (태그는 정확한 캐시 메모리 주소를 알기 위한 정보이며 인덱스를 결합해서 메인 메모리 주소를 찾을수 있다)

어쨌든 메인 메모리보다 현저히 적은 캐시 메모리로 커버를 하기 위해선 충돌을 피할 수가 없으므로 최소화 해야한다. 이러한 최소화를 위해 캐싱라인 종류들을 살펴보자.

## Direct mapped

![Direct mapped](https://www.researchgate.net/profile/Philip-Shirvani/publication/3800962/figure/fig1/AS:669950486274049@1536740051086/Block-diagram-of-a-direct-mapped-cache.png)

Direct mapped는 말 그대로 테이블이 한개라서 같은 태그는 한개의 캐시 공간만 사용할 수 있다. 그래서 캐시 메모리에 데이터가 있는지 찾으려면 비교는 한번만 해도 되서 빠르지만, 충돌이 그만큼 자주 일어나는 단점이 존재한다.

## Fully associative

Fully associative 방식은 말 그대로 태그와 관계없이 비어있는 캐시 메모리를 탐색해서 집어넣고 데이터가 있는지 찾을때도 순차적으로 탐색해서 가져오는 방식이다. 충돌의 위험은 적겠지만 비교할 때마다 순차탐색 시간이 발생하므로 오래걸린다.

## Set associative

![Set associative](https://lh3.googleusercontent.com/proxy/aqAgrY5A-UxM9he-aFx_4cHD6mp4EaUIqmzMupNIiWLeghdU-QS0R1zNLYBuL3oYyWgLC9WlLcvMEQviGvikGzeg1vIx8lboSl1Ug0MpYNQS-cl6N3MUnZSDQdP5Ek4DeUBl1lrTKu-8sJqN6BuvvRZCJEgd3Q)

Fully associative와 Direct mapped의 장점들을 고려하여 만들어진 방식이다. 테이블을 여러개 만들면 같은 태그의 메모리라도 다른 테이블의 태그에 저장하면 되는 원리라서 테이블의 개수에 따라 n-way set associative라고 부른다. (해당 사진은 4 way 방식이다)
이렇게 하면 n번만큼 탐색해서 direct mapped보단 시간이 조금 더 걸리겠지만 충돌위험은 더 줄어들게 된다.
