---
layout: post
title: react-query에 typescript 적용하기 - 리액트 쿼리, 타입스크립트
description: react-query의 hook에 generic을 적용해서 타입스크립트를 최대한 활용하기
categories: Web
tags: [Programming, Web, Frontend, react-query]
---

요즘 리액트 쿼리로 비동기 데이터 fetching을 자주 다루곤 합니다.

그런데 react-query는 타입을 지정하지 않고 사용하게 되면 대부분 데이터가 unknown, any 상태로 사용하게 돼서 어떤 데이터를 다루는지 알기가 어렵습니다.

그래서 react-query는 typescript의 **제네릭** 형식을 강력하게 드라이브 하고 있어서 제네릭 타입만 잘 사용한다면 타입을 보장 받으면서 편리하게 데이터를 다룰 수 있습니다.

그래서 리액트 쿼리에서 자주쓰이는 useQuery, useMutaion의 Generic Type에 대해 다뤄보려 합니다.

## useQuery

---

useQuery의 타입은 아래와 같이 선언되어 있습니다.

```ts
export function useQuery<
  TQueryFnData = unknown,
  TError = unknown,
  TData = TQueryFnData,
  TQueryKey extends QueryKey = QueryKey
>
```

### TQueryFnData, TError

첫 번째의 TQueryFnData는 useQuery로 실행하는 query functiong의 return값을 정하는 제네릭 타입입니다.

그리고 두 번째 TError는 query function의 error 형식을 정하는 제네릭 타입입니다.

```ts
const { data, error } = useQuery<number, AxiosError>(['todo'], getTodo);
// data는 number | undefined의 타입을 가집니다.
// error는 AxiosError | null의 타입을 가집니다.
```

### TData

세 번째 타입은 data에 담기는 실질적 type을 뜻합니다. 방금 첫 번째랑 뭐가 다를까 의문이 들 수 있는데 세 번째 타입은 useQuery의 select option으로 query function의 데이터를 **2차 가공**하는 경우에만 사용하는 type이라고 생각하시면 됩니다.

그래서 세 번째 제네릭 타입을 지정해준다면 data 형식은 세 번째 타입을 따라가고, 지정해주지 않는다면 세 번째 제네릭 타입은 알아서 default인 첫 번째 제네릭 타입을 따라가게 됩니다.

```ts
const { data } = useQuery<Todo[], AxiosError, number>(['todos'], getTodos, { select: todos => todos.length });
// select로 todos의 length을 data에 담기로 했으니,
// 세 번째 제네릭 타입에 number를 필수적으로 넣어줘야 합니다.
```

### TQueryKey

네 번째 타입은 useQuery의 첫 번째 인자로 주는 queryKey의 타입을 명시적으로 지정해주는 제네릭 타입입니다.

default로 지정되는 queryKey 타입은 아래와 같은 형식이라서 query function에서 어떤 key를 받는지 잘 모를수 있습니다.

```ts
type QueryKey = string | readonly unknown[];
// queryKey는 string 혹은 array 형식입니다.
// 하지만 query function에 전달 방식은 항상 array 형식으로 전달 됩니다.
// string => [string]
```

그래서 querykey의 타입을 각 배열마다 어떤 값이 들어갈지 명시적으로 지정해주고 function에 넘겨주고 싶다면 아래와 같이 사용할 수 있습니다.

```ts
const { data } = useQuery<Todo, AxiosError, Todo, [string, number]>(['todos', id], getTodo);
```

## useMutaion

---

useMutaion의 타입은 아래와 같이 선언되어 있습니다.

```ts
export function useMutaion<
  TData = unknown,
  TError = unknown,
  TVariables = void,
  TContext = unknown
>
```

### TData

**TData**는 useMutation에 넘겨준 mutation 비동기 함수의 실행 결과의 타입입니다. 해당 타입은 onSuccess와 data에서 활용할 수 있습니다.

```ts
const { data } = useMutaion<TodoResponse>(postTodo, { onSuccess: res => {} });
// data 타입은 TodoResponse | undefined 입니다.
// onSuccess callback의 res 타입은 TodoResponse 입니다.
```

### TError

**TError**는 useMutation에 넘겨준 mutation 비동기 함수의 에러 결과의 타입입니다.
해당 타입은 onError와 error에서 활용할 수 있습니다.

```ts
const { error } = useMutation<TodoResponse, AxiosError>(postTodo, { onError: err => {} });

// error 타입은 AxiosError | null 입니다.
// onError callback의 err 타입은 AxiosError 입니다.
```

> 참고로 onSettled라는 success와 error 상태를 둘 다 callback으로 다루는 함수도 존재합니다. 해당 함수는 TData, TError 타입을 모두 전달 받습니다.
> ex) onSettled: (res, err)=>{}

### TVariables

**TVariables**는 mutate 함수에 전달할 인자를 지정하는 generic 타입입니다.
해당 타입은 mutate와 각 callback 함수 모두에서 인자로 활용할 수 있습니다.

```tsx
const { mutate } = useMutation<TodoResponse, AxiosError, number>(postTodo, {
  onSuccess: (res, id) => {},
  onError: (err, id) => {},
  onMutate: id => {},
  onSettled: (res, err, id) => {},
});

return <button onClick={() => mutate(5)}>add</button>;
```

위의 코드처럼 mutate함수에 인자로 전달해주는 타입을 지정해주면서 각 callback의 첫 번째 혹은 두세 번째 인자의 타입으로도 활용됩니다.

### TContext

**TContext**는 mutation function을 실행하기 전에 수행하는 onMutate callback 함수의 return값을 지정하는 타입입니다. onMutate의 결과 값을 onSuccess, onError, onSettled에서 활용하려면 해당 타입을 지정해서 활용할 수 있습니다.

```tsx
const { mutate } = useMutation<TodoResponse, AxiosError, number, number>(postTodo, {
  onSuccess: (res, id, nextId) => {},
  onError: (err, id, nextId) => {},
  onMutate: id => id + 1,
  onSettled: (res, err, id, nextId) => {},
});

// onMutate에서 return으로 주는 number형식의 결과를
// onSuccess, onError의 세 번째 인자, onSettled의 네 번째 인자로 받아서 다룰 수 있습니다.
```

---

이 외에도 **useQueryClient**나 **useInfiniteQuery**에서도 제네릭 타입을 활용하면 많은 이점이 존재합니다. useQueryClient의 제네릭 타입은 위 2개 hook 보다 비교적 단순하고 useInfiniteQuery의 제네릭 타입은 useQuery와 유사하기 때문에 useQuery와 useMutation의 제네릭 타입을 잘 활용한다면 잘 다룰 수 있을거라 생각합니다.

아무래도 처음 react-query와 타입스크립트를 도입하면 많은 제네릭 타입들에 대해서 혼란스러울 거라고 생각합니다. 하지만 어느정도 적응기간을 거치다 보면 비동기 상태들의 타입 안정성을 확보할 수 있어서 매우 큰 도움이 될 수 있습니다.
