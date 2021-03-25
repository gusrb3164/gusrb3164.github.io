---
layout: post
title: React Router 라우터 중첩 설정하기
description: 리액트 라우터 설정 법
tags: [Programming, Web, Frontend]
---

리액트를 사용하다 보면 특정 경로로 이동하면서, 하위 컴포넌트를 변화시킬 때가 있다.

이를 위해 지원하는게 Route, Link 이다.
Link 를 통해 특정 경로로 이벤트를 발생할 수 있고, 해당 경로를 Router가 인식해서 적절한 컴포넌트를 띄우게 된다.

html만을 사용할 때는 \<a>태그를 이용했었는데 리액트를 사용할 때는 \<Link>를 사용해야한다. \<a>태그를 사용하면 페이지 전체가 새로 고침이 적용되는데, \<Link> 를 사용하면 Route하는 특정 컴포넌트만 해당 페이지에서 업데이트 할 수 있는 이점이 있다.

```js
import React from 'react';
import { Route } from 'react-router-dom';
import { Header } from '../UI';
import { Home, User, Problem } from '.';

const MainView = () => {
	return (
		<>
			<Header userName={userName} />{' '}
			{/*Header 컴포넌트와 user 버튼은 고정되고, 그 아래 라우트한 컴포넌트들만 바뀌게 된다.*/}
			<Link to={`${match.path}`}>
				<Button>user</Button> {/*유저페이지로 이동*/}
			</Link>
			<Grid>
				<Route exact path="/" component={Home} />
				<Route exact path="/user" component={User} />
				<Route path="/problem" component={Problem} />
			</Grid>
		</>
	);
};
```

위의 예시를 보면 Route 에 exact 가 있는 라인과 exact가 없는 problem 컴포넌트를 route하는 라인이 있는데, 만약 Home component를 Route할 때 exact 를 설정하게 되면 해당 경로와 완전히 일치하는 경우에만 Home 컴포넌트를 띄우게 된다.

반대로 exact를 설정하지 않은 problem 컴포넌트는 /problem 뿐만 아니라 /problem/ex 등 하위 링크가 있어도 Problem 컴포넌트를 띄울수 있게 된다.

위의 두가지 기능을 참고하여 하위 컴포넌트를 라우트 하기 위해 라우트 중첩기능을 사용할 필요가 있을 때는 exact를 명시하지 않고, 나중에 특정 값을 설정하는 /problem/:id 같은 정확한 값을 요구하는 페이지를 라우트 할 때는 exact를 사용하여 정확히 구현해야한다.
