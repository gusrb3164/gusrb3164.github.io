---
layout: post
title: react input lose focus 문제
description: 리액트에서 input 필드에 텍스트를 한 글자만 입력해도 focus가 사라지는 문제 원인
categories: Web
tags: [Programming, Web, Frontend]
---

리액트로 프로젝트를 진행하던중 material ui로 구현한 inputfield에 글자를 입력하려는데 자꾸 한 글자만 입력하면 새로 렌더링 되면서 연속으로 입력이 안되는 문제가 발생했다. 아무리 계속 해결하려고 찾아보는데 거의 8시간을 쓴 것 같다.

스택오버플로에서 계속 찾아본 결과 hook 함수 내에 또 다른 hook 함수를 중첩해서 사용하면 이러한 문제가 발생한다는 것이다. 근데 내가 볼 땐 아무리봐도 중첩이 안됐다고 생각했는데..

알고 보니 material ui에서 제공하는 withStyles가 hook함수라서 그런지? 내가 신경안쓰고 InputField hook함수 안에서 선언해서 이런 문제가 발생했다.

```js
const InputField = ({ text, hint, type, font_size, setValue }: InputProps) => {
	const CssTextField = withStyles({
		root: {
			'&:before': {
				borderBottom: '1px solid white',
			},
			'&:after': {
				borderBottom: '1px solid #00d6af',
			},
			marginBottom: '12px',
			color: 'white',
			width: '60%',
			fontSize: '1.3rem',
			'& input': {
				textAlign: 'center',
			},
		},
	})(Input);
	return (
		<>
			<CssTextField
				onChange={(e: React.ChangeEvent<HTMLInputElement>) => {
					setValue(e.target.value);
				}}
				placeholder={hint}
				startAdornment={
					<InputAdornment position="start">
						<b>{text}</b>
					</InputAdornment>
				}
				type={type}
			/>
		</>
	);
};
```

그래서 결국 아래와 같이 바꿨더니 focus가 사라지는 문제는 잘 해결이 됐다. material ui를 사용하는게 아니더라도 hook함수안에 무심코 hook을 새로 선언 하는 일이 발생할 수 있으니 주의해야 겠다.

```js
const CssTextField = withStyles({
	root: {
		'&:before': {
			borderBottom: '1px solid white',
		},
		'&:after': {
			borderBottom: '1px solid #00d6af',
		},
		marginBottom: '12px',
		color: 'white',
		width: '60%',
		fontSize: '1.3rem',
		'& input': {
			textAlign: 'center',
		},
	},
})(Input);
const InputField = ({ text, hint, type, font_size, setValue }: InputProps) => {
	return (
		<>
			<CssTextField
				onChange={(e: React.ChangeEvent<HTMLInputElement>) => {
					setValue(e.target.value);
				}}
				placeholder={hint}
				startAdornment={
					<InputAdornment position="start">
						<b>{text}</b>
					</InputAdornment>
				}
				type={type}
			/>
		</>
	);
};
```

> 참고 링크<br/> <https://stackoverflow.com/questions/56758864/input-is-loosing-focus-on-hooks-update>
> <https://stackoverflow.com/questions/59715158/react-hooks-input-loses-focus-when-1-character-is-typed-in>
