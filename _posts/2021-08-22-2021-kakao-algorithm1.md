---
layout: post
title: 카카오 코딩 테스트 미로 탈출 Python 풀이
description: 카카오 2021 인턴 블라인드 채용 코딩 테스트 미로 탈출 문제 (다익스트라 + 비트마스크)
categories: Algorithm
tags: [Programming, Algorithm]
---

## 2021 카카오 채용연계형 인턴십 코딩 테스트 4번 미로 탈출

### 문제 설명

<https://tech.kakao.com/2021/07/08/2021-%EC%B9%B4%EC%B9%B4%EC%98%A4-%EC%9D%B8%ED%84%B4%EC%8B%AD-for-tech-developers-%EC%BD%94%EB%94%A9-%ED%85%8C%EC%8A%A4%ED%8A%B8-%ED%95%B4%EC%84%A4/>

### 풀이 방법

해당 문제는 목적지까지의 최단 거리를 구하는 문제이므로 **다익스트라** 알고리즘으로 풀어야 한다.

그런데 함정을 밟을 경우 밟은 노드 주위의 방향이 모두 반대가 되므로 반대 방향의 노드를 다음 우선순위 큐에 넣어야 한다. 따라서 다음에 갈 노드가 반대방향 인지도 그래프에 넣어줘야 인식할 수 있기때문에 처음 그래프 배열에 노드위치와 isReverse의 값을 같이 넣어주었다.

그리고 우선순위 큐에는 경로를 거치는 과정마다 어느 함정이 발동되있는지 상태를 같이 넘겨줘야 하는데 문제 정보에 보면 최대 함정수는 10개라고 나와있다.

따라서 함정의 발동 경우수는 최대 2^10=1024 가지 이므로 해당 가짓수를 효율적인 연산과 메모리 제한을 피하기 위해 **비트마스크** 기법으로 넘겨주면 된다. **(ex: 6개의 함정중 3번, 5번 함정이 발동되면 010100=20)**

#### 요약

1. 그래프에 역방향 노드도 함께 저장한다.
2. 다음 노드가 현재 상태에 따라 갈수 있을때만 우선순위 큐에 넣는다.
3. 넣기 전에 다음 노드가 함정이라면 다음 상태에 해당 함정 비트를 토글한 뒤에 넣어준다.
4. 우선순위 큐에서 꺼낼 때마다 목적지에 해당하는 노드면 결과값과 비교하여 최솟값을 넣어준다.

---

위의 2번과 3번 방식을 함수로 분리하여 최대한 함수형 프로그래밍으로 코드를 작성해봤다.

### 소스 코드

```py
import heapq

INF = float('inf')

# 갈수있는 방향 판별 함수
def isReverse(cur_pos, next_pos, cur_state, traps_idx):
    is_cur_trap_on, is_next_trap_on = False, False
    if cur_pos in traps_idx:
        is_cur_trap_on = (cur_state & (1 << traps_idx[cur_pos])) > 0
    if next_pos in traps_idx:
        is_next_trap_on = (cur_state & (1 << traps_idx[next_pos])) > 0

    # 함정 상태가 같으면 순방향, 다르면 역방향
    return is_cur_trap_on != is_next_trap_on

# 다음 노드를 밟은 후 상태를 반환하는 함수
def getNextState(next_pos, cur_state, traps_idx):
    if next_pos in traps_idx:
        return cur_state ^ (1 << traps_idx[next_pos])
    return cur_state

def solution(n, start, end, roads, traps):
    answer = INF
    min_cost = [[INF for _ in range(n+1)] for _ in range(2**len(traps))]
    traps_idx = {v: i for i, v in enumerate(traps)}
    graph = [[] for _ in range(n+1)]  # end, cost, isReverse
    # graph 생성
    for _start, _end, _cost in roads:
        graph[_start].append([_end, _cost, False])
        graph[_end].append([_start, _cost, True])

    hq = []  # [[sum, cur_pos, trap_state]]
    heapq.heappush(hq, [0, start, 0])
    min_cost[0][start] = 0  # 첫 시작점

    while hq:
        cur_sum, cur_pos, cur_state = heapq.heappop(hq)
        if cur_pos == end:
            answer = min(answer, cur_sum)
            continue
        if cur_sum > min_cost[cur_state][cur_pos]:
            continue
        for next_pos, next_cost, is_reverse in graph[cur_pos]:
            # 못 가는 방향이면 pass
            if is_reverse != isReverse(cur_pos, next_pos, cur_state, traps_idx):
                continue

            next_state = getNextState(next_pos, cur_state, traps_idx)
            next_sum = next_cost+cur_sum
            # 다음 상태의 비용이 이미 더 작으면 pass
            if next_sum >= min_cost[next_state][next_pos]:
                continue

            min_cost[next_state][next_pos] = next_sum
            heapq.heappush(hq, [next_sum, next_pos, next_state])

    return answer
```
