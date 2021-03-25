---
layout: post
title: 자바스크립트 클로저 개념잡기
description: 자바스크립트의 특징중 하나인 클로저 이해하기
tags: [Programming, javascript]
---

# 클로저란?

### 외부 함수의 실행이 끝나고 외부 함수가 소멸된 이후에도 내부 함수가 외부 함수의 변수에 접근할 수 있는 구조

---

해당 정의만 가지면 이해가 잘 안되기 때문에 예시를 살펴보자

```js
function makeFunc() {
	var name = 'hello';
	function displayName() {
		alert(name);
	}
	return displayName;
}

var myFunc = makeFunc();
myFunc(); // name이 undefined로 예상되지만 실제로는 hello 를 alert 하게 됨
```

위의 myFunc 변수를 선언하게되면 makeFunc() 함수는 displayName 함수를 리턴하게 되고 소멸하여 name 변수또한 사라지는게 보통 언어의 방식이다. 하지만 자바스크립트에서는 함수내의 변수를 해당 함수 속 또다른 함수가 사용하게 되면 closure라는 방식에 의해 함수 속 함수에서는 계속 사용이 가능하게 된다.

---

# 이러한 클로저를 왜 알아야 할까?

### 클로저는 데이터와 함수를 연결시켜준다는 맥락에서 객체와 비슷하게 사용할 수 있다. 그리고 객체와 비슷하게 사용한다는 것은 자바 언어에서 사용하는 private 메소드를 클로저를 통해 자바스크립트에서 구현할 수 있다는 걸 의미한다.

<br>
아래의 예시를 살펴보자.

```js
function myName(name) {
	return {
		getName: function () {
			return name;
		},
		setName: function (_name) {
			name = _name;
		},
	};
}
var tmp = myName('hello');
console.log(tmp.getName()); //hello 출력
tmp.setName('hi');
console.log(tmp.getName()); //hi 출력
```

tmp 의 변수에 getName과 setName 함수가 담긴 객체가 저장되어있다.
myName함수는 return 후에 소멸된 상태로 tmp.getName()을 호출하면 그대로 name 값을 가져올 수 있게 된다.
이 상태에서 우리는 name 변수를 getName, setName을 통해서만 조작할 수 있는 private 상태로 관리할 수 있게 된다. 클로저는 이러한 이점을 가지고 있다.

---

## 참고 문서

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)

[생활코딩](https://opentutorials.org/course/743/6544)
