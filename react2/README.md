# React 19

- use
  훅스의 룰의 영향을 받지 않으면서 프라미스와 컨텍스트를 넘길 수 있다.
  넘기게 되면 프라미소가 해결될 떄까지 중단된다. 약간 api 콜을 위해 쓸 수 있거나 혹은 리액트 서버 액션의 역할을 대신할 수 있을 것 같다.
  if와 같은 조건절이나 루프 안에서도 쓸 수 있으니깐 기억

- Form Action
  <form> 태그에 action을 넣을 수 있다.
  React Server Action과는 다르다. 그냥 Ajax 폼 처리를 간소화시켜주는 역할을 한다.

- useFormState
  Form Action도 같이 연동해서 사용해야 함.
  상위에 있는 <form>이 현재 제출 중인지 또는 성공적으로 제출되었는 지를 알려준다.

  ```typescript
  const { pending, data, method, action } = useFormStatus();
  ```

- useOptimisitic
  `react-query`와 같이 낙관적 업데이트를 하게 도와줌.

  ```typescript
  import { useOptimistic } from "react";

  function AppContainer() {
    const [optimisticState, addOptimistic] = useOptimistic(
      state,
      // 업데이트 함수
      (currentState, optimisticValue) => {
        // 현재 상태에 낙관적인 값을 합치고 새로운 상태를 반환
      }
    );
  }
  ```

- React Transition API (startTransition)
  이전에 진행 중이던 애니메이션을 중단하고 바로 다음 애니메이션을 실행시킨다.
  예를 들어, 탭 애니메이션의 경우 탭 1의 애니메이션이 진행 중일 때 유저가 탭 2 애니메이션을 호출하면 탭 1의 애니메이션을 바로 중단시키고 탭 2의 애니메이션을 실행시키는 것이다.

# React v19 마이그레이션

- 함수형 컴포넌트에서 propTypes및 defaultProps의 삭제
- 클래스 컴포넌트에서 contextTypes 및 getChildContext의 삭제
- string refs 삭제
- Module pattern factories 삭제
- React.createFactory 삭제
- ReactDOM.render와 ReactDOM.hydrate의 삭제
- ReactDOM.findDOMNode 삭제
- React Strict의 변경
- UMD build 삭제
- ref의 암시적 반환 사용 중지

# 왜 useEffect에서 데이터 패칭을 하면 안될까?

`useEffect`가 의존성 배열이 변경된 것을 감지하는 원리는 다음과 같다.

1. 함수형 컴포넌트가 렌더링될 때마다 useEffect 안에 있는 콜백 함수가 새로 생성
2. 이전 렌더링에서 생성된 useEffect의 의존성 배열과 현재 렌더링에서 생성된 useEffect의 의존성 배열을 비교
3. 만약 두 의존성 배열이 서로 다르다면, 의존성 배열에 있는 값들이 변경된 것으로 간주하고 해당 콜백 함수를 실행

즉, `useEffect`는 데이터 바인딩, 옵저버 같은 특별한 기능을 통해 값의 변화를 관찰하는 것이 아니다. 또한 클래스 컴포넌트의 라이프 사이클도 완벽히 대체하는 것도 아니다.

`useEffect`에서 데이터 패칭을 하면 안되는 이유는 `경쟁 상태` 때문이다. -> 콜백 함수로 들어온 비동기 함수의 응답 속도에 따라 최종 결과가 이상하게 나타날 수 있기 때문이다.

[참고 자료](https://velog.io/@sxin2949/%EC%99%9C-useEffect%EC%97%90%EC%84%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8C%A8%EC%B9%AD%EC%9D%84-%ED%95%98%EB%A9%B4-%EC%95%88%EB%90%A0%EA%B9%8C)

# 리액트의 state는 불변하다.

리액트는 상태(state)를 설정할 때는 새로운 값을 전달하지 않으면 컴포넌트를 다시 렌더링하지 않습니다.

리액트에서는 이전 상태와 새로운 상태를 비교하기 위해 `===`(일치연산자)와 비슷한 것을 사용합니다. 물론 문서에서는 `===` 대신 `Object.is()`를 사용한다고 설명하고 있지만, 이 둘은 기본적으로 동일합니다.

> 둘 다 얕은 동등성 검사를 수행하며, 이것이 객체와 배열을 단순히 변경해도 상태 변경을 알리지 않는 이유입니다.

변경사항을 반영하여 새로운 참조를 만들지 않으면 리액트가 상태 변경을 인지할 수 없습니다. 따라서 리액트는 얕은 동등성 검사를 충족시키기 위해 변경된 객체와 배열의 완전히 _**새로운 복사본이 필요합니다.**_

```typescript
function addUser(newUser) {
  users.push(newUser);
  setUsers(users);
}
```

이렇게 해도 배열의 참조는 여전히 동일하기 때문에 리렌더링이 되지 않는다.

해결을 하려고 하면

```typescript
function addUser(newUser) {
  // 리렌더링이 성공적으로 작동합니다.
  setUsers([...users, newUser]);
}

function addUser(newUser) {
  // 리렌더링이 성공적으로 작동합니다.
  setUsers(users.concat(newUser));
}
```

이렇게 해야 합니다.

# 동시성모드(Concurrent Mode)는 여러 React기능들과 호환가능하다.

Atomic한 상태 관리는 React의 동시성 모드(Concurrent Mode)와 같은 고급 기능들과 잘 호환되는 것으로 알려져 있습니다. 이러한 호환성은 여러 요인에 기반합니다:

### `Atomic한 상태 관리란?`

Atomic한 상태 관리는 상태를 최소 단위로 나누어 관리하는 방식을 말합니다. 이 방식에서 상태는 작은 단위(아톰)로 분리되며, 각 아톰은 독립적으로 업데이트되고 구독될 수 있습니다. 예를 들어, Recoil과 같은 라이브러리는 이러한 접근 방식을 채택하고 있습니다.

### `React의 동시성 모드(Concurrent Mode)`

동시성 모드는 React의 실험적인 기능으로, UI 렌더링 작업을 중단하고, 우선순위에 따라 다른 작업을 처리한 후 다시 렌더링 작업을 재개할 수 있는 능력을 제공합니다. 이 모드는 사용자 경험을 향상시키고, 대규모 업데이트 도중에도 애플리케이션이 반응성을 유지하도록 돕습니다.

### `호환성의 이유`

1. `독립적인 상태 업데이트`: Atomic한 상태 관리는 상태를 개별적인 단위로 나누어 관리합니다. 이는 React의 동시성 모드에서 다루는 여러 비동기 작업과 잘 맞음. 상태의 각 부분이 독립적으로 업데이트되므로, React가 비동기 작업을 수행하면서도 상태의 일관성을 유지하기 쉬움
2. `우선순위 기반 업데이트`: 동시성 모드에서는 작업에 우선순위를 부여하여 중요한 업데이트를 먼저 처리할 수 있다.
   Atomic한 상태 관리는 이러한 우선순위 기반 업데이트와 잘 호환. 상태의 특정 부분이 변하면, 해당 상태에 의존하는 컴포넌트만 우선적으로 업데이트될 수 있다.
3. `리소스 효율성`: Atomic한 상태 관리는 필요한 상태만 정확하게 업데이트. 이는 동시성 모드에서 중요한 이점이며, 불필요한 리소스 사용을 줄이고 애플리케이션의 반응성을 향상시킴
4. `불필요한 리렌더링 방지`: Atomic한 상태 관리는 상태 변경 시 관련된 컴포넌트만 리렌더링하므로, 동시성 모드에서 중단과 재개가 발생할 때 불필요한 리렌더링을 방지

### `결론`

이러한 이유로, Atomic한 상태 관리 시스템은 React의 동시성 모드와 같은 고급 기능과 잘 호환된다.
이는 더 나은 사용자 경험과 애플리케이션의 성능 향상에 기여할 수 있다. 그러나 React의 동시성 모드는 여전히 실험적인 단계에 있으며, 해당 기능을 사용할 때는 관련 문서와 업데이트를 주의 깊게 확인하는 것이 중요함.
