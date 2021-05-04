---
layout: post
title: red black tree의 개념과 특징
description: 자료구조에서 레드블랙트리의 개념과 특징
categories: Computer-Science
tags: [Data-Structure, CS]
---

## Red Black Tree

RBT는 BST를 기반으로하는 트리 형식의 자료구조이다. 데이터의 Search, Insert, Delete에 해당하는 시간복잡도가 모두 O(log n)에 해당하는 특징을 가진다. 이러한 이유는 트리의 depth를 최대한 줄이는 것에 초점을 맞췄기 때문이다. 즉, 완전 이진 트리 구조를 유지하려고 하는 자료구조로 볼 수 있다.

> 기존의 BST 트리는 삽입 삭제 과정에서 트리의 균형이 무너져 높이가 최대 n까지 구성될 수 있는 문제점이 있다.

## 특징

1. 각 노드는 Red or Black 이라는 색을 가진다.
2. Root Node 의 색은 Black이다.
3. 각 Leaf Node 는 모두 Black이다.
4. 어떤 노드의 색이 Red라면 두 개의 children의 색은 모두 Black 이다. (Red가 연속으로 위치할 수 없다)
5. 각 노드에 대해서 노드로부터 descendant leaves 까지의 단순 경로는 모두 같은 수의 black nodes 들을 포함하고 있다. 이를 해당 노드의 Black-Height라고 한다.
6. 노드의 child가 없을 경우 child를 가라키는 포인터 NIL을 저장하고 NIL들을 Leaf Node로 간주한다.

> 참고자료 : <https://www.youtube.com/watch?v=qvZGUFHWChY>
