---
layout: post
title: MVC, MVP, MVVM 패턴 개념과 특징 이해하기
description: MVC, MVP 그리고 프론트엔드 구조에 자주 쓰이는 MVVM 패턴을 이해하자
categories: Web
tags: [Programming, frontend]
---

### 예전에 클라이언트 채용공고에 우대사항 중에 하나가 "MV\* 패턴을 이해하고 있는자" 라는 문구가 눈에 띄였다

### 기본적으로 잘 알려진 MVC 패턴은 원리만 알고는 있었는데 그 외에 어떤게 있는지 잘 이해는 못 했었다. 클라이언트 부분의 작업과 유지보수를 유용하게 해주는 MV\* 패턴에는 뭐가 있고, 프론트엔드를 구성하는데 있어서 어떻게 다른지 살펴보자

<br>

---

### 설명에 앞서 이해를 돕기 위해 사용자가 일종의 form 을 작성하고 제출(데이터 or Action을 주는 행위)하고 새로운 데이터를 받아와야 하는 상황이라고 가정하자.

---

# MVC

![MVC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F7IE8f%2FbtqBRvw9sFF%2FAGLRdsOLuvNZ9okmGOlkx1%2Fimg.png)

아무래도 MV\* 패턴중에선 가장 잘 알려져 있어서 정보처리기사에서도 개념으로 나오는 패턴이다.
해당 패턴에서 각각의 역할은 아래와 같다.

```md
Model : 앱이 포함해야할 데이터가 무엇인지를 정의한다. 데이터가 변경되면 모델을 일반적으로 뷰에게 알리며 가끔 컨트롤러에게 알린다.

View : 앱의 데이터를 보여주는 방식을 정의한다. 해당 데이터는 모델한테 바로 받거나 컨트롤러를 통해 받는다.

Controller : Client로부터 응답을 받아서 Model 또는 View를 업데이트 하거나, 데이터를 전달해주는 역할을 한다.
```

이제 가정 상황을 고려해서 MVC의 처리 순서를 살펴보자.

1. 사용자가 제출한 form 데이터가 Controller에게 전달된다.
2. Controller는 Model 에게 전달 하고 새로운 데이터를 요청한다.
3. Model 은 로직을 처리하여 새로운 데이터를 연관된 View 에게 알린다.
4. View는 받은 데이터를 통해 화면을 업데이트 한다.
5. 때로는 Controller가 View의 데이터를 직접 처리해서 View에게 알리는 경우도 있다. (ex: 단순히 기존 데이터를 정렬하는 경우)

```md
장점 : 다른 패턴들에 비해 직관적인 구조로 상대적으로 이해하기가 쉽다.

단점 : View 와 Model 사이의 업데이트 관계가 있다 보니 의존성이 높아진다. 이 때문에 프로젝트의 규모가 커지면 복잡해진다.

특징 : Controller 와 View 가 1:N 의 구조로 연결된다.
```

# MVP

![MVP](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FclZlsT%2FbtqBTLzeUCL%2FIDA8Ga6Yarndgr88g9Nkhk%2Fimg.png)

MVP 패턴은 MVC에서 Controller가 사라지고 Presenter가 생긴 구조이다. 그리고 처음 데이터의 요청은 View로 들어오게 된다.

```md
Model : 앱이 포함해야할 데이터가 무엇인지를 정의하며, 데이터를 처리한다.

View : 앱의 데이터를 보여주는 방식을 정의한다. 요청이 들어오면 Presenter 에게 알린다.

Presenter : Model 과 View 의 중계자 역할을 한다. 서로의 요청과 응답을 전달해준다.
```

가정 상황을 고려해서 MVP의 처리 순서를 살펴보자.

1. 사용자가 제출한 form 데이터가 View에게 전달된다.
2. View가 Presenter에게 데이터를 전달하며 새로운 데이터를 요청한다.
3. Presenter는 Model에게 데이터를 전달하며 새로운 데이터를 요청한다.
4. Model은 Presenter의 요청을 토대로 데이터를 처리하여 새 데이터를 Presenter에게 전달한다.
5. Presenter는 받은 데이터로 View에게 알린다.
6. View는 받은 데이터를 통해 화면을 업데이트 한다.

```md
장점 : View 와 Model 사이의 연결점이 없어서 의존성문제가 사라진다.

단점 : 대신 Presenter 역할이 중요해지면서 View와 Presenter 사이의 의존성 문제가 생긴다.

특징 : Presenter 와 View 가 1:1 의 구조로 연결된다.
```

# MVVM

![MVVM](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCiXz0%2FbtqBQ1iMiVT%2FstaXr7UO95opKgXEU01EY0%2Fimg.png)

MVVM 패턴은 기존과 동일한 Model, View 그리고 새로운 ViewModel로 만든 구조이다. 현재 SPA (Single Page Application)의 클라이언트측 기능이 중요해지면서 자주쓰이는 패턴이다.

```md
Model : 앱이 포함해야할 데이터가 무엇인지를 정의한다.

View : 앱의 데이터를 보여주는 방식을 정의한다. View에 필요한 데이터는 ViewModel을 구독하여 받고 있다.

Controller : View로부터 요청을 받은 데이터를 처리하여 Model을 갱신하고 자신이 소유한 데이터도 갱신한다.
```

가정 상황을 고려해서 MVVM의 처리 순서를 살펴보자.

1. 사용자가 제출한 form 데이터가 View에게 전달된다.
2. View는 ViewModel에게 해당 데이터를 특정 요청과 전달한다.
3. ViewModel은 받은 데이터를 로직에 따라 처리하여 Model을 업데이트한다.
4. ViewModel은 또한 자신이 Model과 연관하여 저장하고 있는 데이터를 갱신한다.
5. 갱신된 데이터를 구독(Data Binding 혹은 Observing)하고 있는 View들은 변경 상태를 인식하고 업데이트한다.

```md
장점 : ViewModel이 따로 저장하는 데이터를 구독하여 자동 업데이트 하는 구조로서, View가 독립적으로 변함으로써 Model, ViewModel 과의 의존성 문제를 해결했다.

단점 : 다른 패턴에 비해 상대적으로 이해가 어렵고 처음 설계가 복잡하다.

특징 : ViewModel 과 View가 1:N 의 구조로 연결된다.
```

이처럼 클라이언트의 규모가 커지면 MV\* 패턴의 구조를 잘 알고 선정하는 것이 프로젝트 관리에 많은 도움이 되므로 직접 프로젝트에 적용해보는 경험이 중요하다.

따라서, 현재 중요하고 자주 쓰이는 MVVM 구조를 React.js에서 Context api 를 통해 다음 글에서 작성해보자.

※ 사진 출처 및 참고자료 [https://beomy.tistory.com/43]
