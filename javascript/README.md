# 화살표 함수에 this 객체가 없으므로써 예상 가능한 오류에 대해서 설명해주세요.

- DOM 이벤트 리스너 & 핸들러로 사용 시

DOM 요소의 이벤트 리스너 & 핸들러로 화살표 함수를 사용하면 ‘this’가 예상과 다른 값을 가질 수 있다.

일반적인 함수로 정의한 경우 this는 이벤트가 발생한 DOM 요소를 가리킨다. 하지만 화살표 함수를 사용하면 주변 스코프의 this를 가져오므로 예상과 다른 결과가 나타날 수 있다. (window를 가리킨다고 알고 있음. 화살표 함수는 자체적으로 this를 바인딩하지 않기 때문에, 부모 함수나 객체의 this 값을 그대로 사용하게 됨.)

```jsx
const button = document.getElementById("myButton");
button.addEventListener("click", () => {
  console.log(this); // window 객체를 가리킴, 예상과 다름
});
```

- 생성자 함수로 사용시

화살표 함수는 생성자 함수로 사용될 수 없다. 생성자 함수는 <code>새로운 객체를 생성하고 this를 해당 객체로 바인딩하는 역할</code>을 한다. 화살표 함수에서는 <code>this</code>를 자체적으로 가지지 않기 때문에 이러한 용도로 사용할 수 없다.

<code>
const Person = (name) => {
  this.name = name; // 에러 발생
};
const person = new Person("John");
</code>

객체 메소드로 사용시 오류 발생

화살표 함수를 객체의 메소드로 사용할 때, 메소드 내에서 <code>this</code>는 해당 객체가 아닌 주변 스코프의 <code>this</code>를 가르킨다. 이로 인해 메소드 내에서 객체의 속성에 접근하거나 수정할 때 문제가 발생한다.

```jsx
const obj = {
  value: 42,
  getValue: () => {
    console.log(this.value); // undefined
  },
};
```

그럼에도 왜 화살표 함수가 react hooks에서 자주 쓰일까?
화살표 함수는 코드를 좀 더 간결하게 만들어준다. 하지만 간결해진 대신 컴포넌트에서 라이프사이클을 활용하지 못한다는 단점이 있다. 이런 종류의 컴포넌트는 stateless <code>함수형 컴포넌트(functional component)</code>라 불린다. → 중괄호도 생략가능해서 간결하다.

# 화살표 함수랑 다른 일반 함수의 차이점은?

참고로 리액트컴포넌트 만들 때 함수 선언식으로 만드는 것과 화살표 함수로 만드는 것에 큰 차이점은 없고 프로젝트의 명세를 따른다.

주요 차이점은 *호이스팅*에서 차이가 발생한다.

- 함수 선언식은 함수 전체를 호이스팅한다. 따라서 정의된 범위의 맨 위로 호이스팅되어서 함수 선언 전에 함수를 사용할 수 있다.
- 화살표 함수는 Lexical하게 언제나 상위 스코프의 this를 가르킨다. 따라서 함수를 선언할 때 this에 바인딩할 객체가 정적으로 결정된다.
- 함수 표현식은 별도의 변수에 할당하게 되는데, 변수는 선언부와 할당부를 나누어 호이스팅하게 된다. 따라서 선언부만 호이스팅하게 된다.

```jsx
function sum() {} // 함수 선언식
const sum = () => {}; // 화살표 함수
const sum = function (a, b) {}; // 함수 표현식
```

## 화살표 함수와 일반 함수의 주요 차이점

- 일반 함수는 생성자 함수로 사용할 수 있고, 화살표 함수는 생성자로 함수를 사용할 수 없다. 따라서 prototype 프로퍼티를 가지고 있지 않다.
- 일반 함수에서는 함수가 실행될 때 암묵적으로 arguments 객체가 전달되어 사용할 수 있다.
- 화살표 함수에서는 arguments 객체가 전달되지 않는다.

```jsx
function fun() {
  console.log(arguments); // Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
}
```

```jsx
const arrFun = () => {
  console.log(arguments); // Uncaught ReferenceError: arguments is not defined
};

fun(1, 2, 3); // error !!!
```

일반함수는 arguments 객체가 전달되어 [1,2,3]이 찍히지만

화살표함수는 arguments를 정의할 수 없다고 뜬다.

# Rest parameter란?

함수 내부에서 사용하는 `arguments` 객체를 단점을 보완하기 위해 ES2015 이후로 나온 argument를 배열 안에서 사용할 수 있는 parameter이다. 유사 배열인 arguments와는 다르게 배열의 메소드를 사용할 수 있고 argument들을 묶어서 따로 사용할 수 있다. 또한 일반 parameter와 함께 쓸 때는 마지막에 남은 parameter들을 묶어주는 역할로써 사용한다.

```jsx
function 함수(first, second, ...others) {
  동작;
}
```

# 얕은 복사 vs 깊은 복사에 대해서 설명해주세요.

- 얕은 복사 (Shallow Copy):
  (배열이나 함수도 얕은 복사, 깊은 복사 가능 -> 그냥 참조가 복사된다고 이해하면 될듯)
  얕은 복사는 객체를 복사할 때, 원본 객체의 프로퍼티들을 새로운 객체로 복사하지만, 프로퍼티 값이 객체인 경우에는 참조를 공유합니다.
  얕은 복사를 통해 복사된 객체와 원본 객체는 부분적으로 공유된다고 생각할 수 있습니다.
  주로 Object.assign(), 전개 연산자 (...)를 사용하여 얕은 복사를 수행합니다.
  예시:

```jsx
const originalObj = { a: 1, b: { c: 2 } };
const shallowCopy = Object.assign({}, originalObj);

originalObj.b.c = 3;

console.log(shallowCopy.b.c); // 출력: 3 (객체 내용이 공유됨)
```

- 깊은 복사 (Deep Copy):

깊은 복사는 객체를 완전히 복제하여, 원본 객체와 완전히 분리된 복사본을 만듭니다. 복사된 객체 및 해당 하위 객체는 모두 독립적으로 존재합니다.
주로 재귀 함수나 외부 라이브러리(예: lodash의 <code>\_.cloneDeep()</code>)를 사용하여 깊은 복사를 수행합니다.
예시:

```jsx
const originalObj = { a: 1, b: { c: 2 } };
const deepCopy = JSON.parse(JSON.stringify(originalObj));

originalObj.b.c = 3;

console.log(deepCopy.b.c); // 출력: 2 (객체가 완전히 복사됨)
```

얕은 복사로 생성된 객체 안에 객체나 배열을 변경하게 될 경우 원본의 객체나 배열의 참조가 복사가 되므로, 원본 객체 & 배열과 복사된 객체 & 배열은 같은 값을 참조하게 된다. (서로 값을 공유하게 된다.) -> 따라서 하나만 변경해도 둘다 같이 변경된다. (서로 영향을 받게 된다.)

깊은 복사를 사용하면 원본 객체와 복사본 간에 어떤 프로퍼티도 공유되지 않으므로 변경 사항이 서로에게 영향을 주지 않습니다. 그러나 깊은 복사는 중첩된 객체나 배열이 많은 경우에는 성능 면에서 부담이 될 수 있으며, JSON 문자열로 변환 후 다시 파싱하는 방법은 함수나 순환 참조 등을 처리하지 못할 수 있습니다. 따라서 복사 방법을 선택할 때 상황을 고려해야 합니다.

# 동등 연산자(==)와 일치 연산자의 차이점(===)을 설명해주세요.

동등 연산자(==)는 두 값을 비교할때 형변환을 수행한다. 즉, 두 값의 데이터 타입이 달라도 자동으로 형변환 수행 후 비교한다.
일치 연산자(===)는 두 값이 데이터 타입과 값이 모두 같은지 비교하기 때문에 형변환은 일어나지 않는다.
따라서, 동등 연산자를 사용할때는 형변환까지 고려하고 사용해야 나중에 예기치 못할 오류를 예방할 수 있다.

# 왜 호이스팅이 일어나고 호이스팅이 일어남으로써 발생 가능한 오류 및 수정 방법?

호이스팅(Hoisting)은 JavaScript의 동작 방식 중 하나로, 변수 및 함수 선언이 코드의 상단으로 "끌어 올려"지는 현상을 가리킵니다. 이것은 코드 실행 전에 JavaScript 엔진에 의해 처리됩니다. 호이스팅은 코드 작성자에게 예상치 못한 동작을 유발할 수 있으므로 주의가 필요합니다.

호이스팅이 발생하는 이유는 JavaScript의 컴파일 단계에서 변수 및 함수 선언을 처리하는 방식과 관련이 있습니다. JavaScript 엔진은 코드를 실행하기 전에 변수와 함수 선언을 메모리에 등록하고 초기화합니다. 따라서 코드 실행 중에 변수와 함수를 참조할 수 있습니다.

예를 들어 호이스팅이 발생하는 경우를 살펴보겠습니다. 다음은 호이스팅 예제입니다:

```jsx
console.log(x); // undefined
var x = 5;
console.log(x); // 5
```

이 코드에서 `console.log(x)`를 첫 번째로 호출할 때 `x`는 선언되지 않았지만 오류가 발생하지 않고 `undefined`가 출력됩니다. 이는 호이스팅으로 인해 변수 `x`가 선언되었지만 아직 값이 할당되지 않았기 때문입니다. 따라서 코드 실행 전에 변수 `x`가 끌어올려져 선언된 것처럼 동작합니다.

호이스팅은 함수 선언에 대해서도 적용됩니다. 다음은 함수 호이스팅의 예입니다:

```jsx
foo(); // "Hello, World!"

function foo() {
  console.log("Hello, World!");
}
```

이 코드에서 `foo` 함수를 호출하기 전에 함수를 선언했습니다. 이것은 호이스팅으로 인해 함수 선언이 코드 상단으로 끌어올려져 함수를 호출하기 전에도 함수를 참조할 수 있게 됩니다.

호이스팅에 주의해야 하는 이유는 예상치 못한 결과를 초래할 수 있기 때문입니다. 예를 들어, 변수를 선언하기 전에 사용하면 `undefined`가 될 수 있으며, 이로 인해 오류가 발생할 수 있습니다. 따라서 변수 및 함수 선언을 코드의 상단에 명시적으로 작성하고, 호이스팅을 이해하여 코드를 더 예측 가능하게 작성하는 것이 좋습니다.

# iterable 객체에 대해서 설명해주세요.

- <code>for .. of</code>을 사용할 수 있는 객체를 `iterable` 객체라고 한다.

```tsx
let range = {
  from: 1,
  to: 5,
};

range[Symbol.iterator] = function () {
  return {
    current: this.from,
    last: this.to,

    next() {
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    },
  };
};

for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```

- <code>range</code>를 이터러블로 만들려면(for..of가 동작하도록 하려면) 객체에 <code>Symbol.iterator</code>(특수 내장 심볼)라는 메서드를 추가해 아래와 같은 일이 벌어지도록 해야한다.
- 이터러블 객체의 핵심은 <code>관심사의 분리(Separation of concern, SoC)</code>에 있다.

- <code>range</code>엔 메서드 <code>next()</code>가 없다.
  대신 range[Symbol.iterator]()를 호출해서 만든 ‘이터레이터’ 객체와 이 객체의 메서드 next()에서 반복에 사용될 값을 만들어낸다.

# 제너레이터에 대해서 설명해주세요.

제너레이터는 이터레이터를 어떻게 하면 쉽게 구현할지를 염두에 두며 추가되었다.

일반 함수는 하나의 값만 리턴할 수 있다. 하지만 제너레이터를 사용하게 되면 여러 개의 값을 리턴할 수 있다. function 뒤에 \*를 붙여서 사용 가능하다. 함수 안에서 yield를 통해서 여러 개의 값을 필요에 따라 하나씩 리턴할 수 있다.

next() 메소드를 통해서 실행 순서를 제어할 수 있다. (가장 가까운 `yield <value>`문을 만날 때까지 실행)

next()는 항상 두 프로퍼티를 가진 객체를 반환한다.

- done: true 혹은 false
- value: 산출 값

일반적으로 값을 할당하게 되면 (let generator = generateSequence())코드가 실행되지 않고 실행을 처리하는 제너레이터 객체를 리턴한다.

### <code>`function* f(…)`</code>가 맞나요 아니면 <code>`function *f(…)`</code>가 맞나요?

둘 다 맞습니다.

그런데 `*`는 제너레이터 `함수`를 나타내므로 대개는 첫 번째 문법이 선호됩니다. `*`는 종류를 나타내는 것이지 이름을 나타내는 것이 아니기 때문입니다. 그러므로 `*`는 `function`에 붙이도록 하자.

### `next()` 메서드를 보면서 짐작하셨듯이, 제너레이터는 [이터러블](https://ko.javascript.info/iterable) 이다.

따라서 `for..of` 반복문을 사용해 값을 얻을 수 있다.

```jsx
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

let generator = generateSequence();

for (let value of generator) {
  alert(value); // 1, 2가 출력됨
}
```

그런데 주의할 점은 여기서 value를 리턴하게 했는데 for..of 이터레이션이 done: true일 때 마지막 value를 무시하기 때문에 1,2만 출력된다.

만약에 다 리턴하고 싶다면 yield를 리턴해야 한다.

보통은 다음과 같이 된다.

```jsx
function* fetchSequentially(urls) {
  for (const url of urls) {
    const response = yield fetch(url);
    const data = yield response.json();
    console.log(data);
  }
}

const urls = ["url1", "url2", "url3"];
const generator = fetchSequentially(urls);

function runGenerator() {
  const { value, done } = generator.next();
  if (!done) {
    value.then((result) => {
      const { value, done } = generator.next(result);
      if (!done) {
        value.then((result) => {
          runGenerator(); // 재귀 호출을 통해 계속 실행
        });
      }
    });
  }
}

runGenerator();
```

여기서 return 값을 따로 명시해 주지 않았으므로 마지막에는 done:true, value: undefined가 된다.

여기서 보듯이 generator는 api url를 여러번 호출하게 될 때 유용하게 쓰일 수 있다.

### 제너레이터 컴포지션

제너레이터 컴포지션(generator composition)은 제너레이터 안에 제너레이터를 '임베딩(embedding, composing)'할 수 있게 해주는 제너레이터의 특별 기능 (제너레이터를 다른 제너레이터에 끼워 넣을 수 있다.

```jsx
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generatePasswordCodes() {
  // 0..9
  yield* generateSequence(48, 57);

  // A..Z
  yield* generateSequence(65, 90);

  // a..z
  yield* generateSequence(97, 122);
}

let str = "";

for (let code of generatePasswordCodes()) {
  str += String.fromCharCode(code);
}

alert(str); // 0..9A..Za..z
```

이 코드가

```jsx
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generateAlphaNum() {
  // yield* generateSequence(48, 57);
  for (let i = 48; i <= 57; i++) yield i;

  // yield* generateSequence(65, 90);
  for (let i = 65; i <= 90; i++) yield i;

  // yield* generateSequence(97, 122);
  for (let i = 97; i <= 122; i++) yield i;
}

let str = "";

for (let code of generateAlphaNum()) {
  str += String.fromCharCode(code);
}

alert(str); // 0..9A..Za..z
```

이런 식으로 바뀐다.

# 실행 컨텍스트에 대해서 설명해주세요.

코드가 실행될 때 생성되는 환경을 나타내는 개념입니다. 실행 컨텍스트는 변수, 함수 선언, 스코프, this 등과

같은 정보를 관리하며 코드의 실행을 지원합니다. 실행 컨텍스트는 크게 세 가지 종류로 나눌 수 있다.

### 실행 컨텍스트의 종류 3가지

1. 전역 실행 컨텍스트
   1. 코드가 실행되면 가장 먼저 전역 실행 컨텍스트가 생성.
   2. 전역 실행 컨텍스트는 어플리케이션 전체를 범위로 하며, 전역 스코프(Global Scope)를 가지고 있다.
   3. 전역 실행 컨텍스트에는 전역 변수와 전역 함수가 포함.
2. 함수 실행 컨텍스트
   1. 함수가 호출될 때마다 해당 함수의 실행 컨텍스트가 생성
   2. 각 함수 실행 컨텍스트는 함수 내부의 로컬 변수, 매개변수, 내부 함수 선언 등을 저장
   3. 함수 실행 컨텍스트는 자신의 변수 환경과 상위 스코프에 대한 참조를 유지
3. eval 실행 컨텍스트
   1. eval() 함수로 실행되는 코드 블록에 대한 실행 컨텍스트.

# 이벤트 버블링에 대해서 설명해주시고 방지하는 방법에 대해서 알려주세요.

이벤트 버블링이란 한 요소에 이벤트가 발생하면 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작하고 최상단의 부모 요소를 만날 때까지 반복되면서 핸들러가 동작하는 현상을 말한다.

- 예제 코드

```jsx
<body>
  <div class="DIV1">
    DIV1
    <div class="DIV2">
      DIV2
      <div class="DIV3">DIV3</div>
    </div>
  </div>
</body>;

const divs = document.querySelectorAll("div");

const clickEvent = (e) => {
  console.log(e.currentTarget.className);
};

divs.forEach((div) => {
  div.addEventListener("click", clickEvent);
});
```

- 이 코드에서 div를 클릭하면 해당 클래스의 이름이 콘솔로 출력되는 코드인데, 자바스크립트는 기본적으로 버블링이 발생하기 때문에 `<div class="DIV3">DIV3</div>`를 클릭한다면 콘솔에는 DIV3, DIV2, DIV1이 순서대로 출력이 될 것이다.

이벤트 버블링을 중단시키려면 일반적으로 JavaScript에서 `event.stopPropagation()` 메서드를 사용하거나 이벤트 핸들러에서 `return false`를 반환하면 됩니다. 이렇게 하면 이벤트가 더 이상 상위로 전파되지 않는다.

### 이벤트 버블링이 존재하는 이유

이벤트 전파 및 위임: DOM 트리의 요소들은 중첩된 구조를 가질 수 있습니다. 이때 이벤트 버블링은 이벤트가 하위 요소에서 상위 요소로 전파되는 메커니즘을 제공하여, 중복된 이벤트 핸들러, 리스너를 등록하지 않고도 상위 요소에서 이벤트를 처리할 수 있도록 도와줍니다. 이것을 이벤트 위임(Event Delegation)이라고 하며, 효율적인 이벤트 관리와 메모리 사용을 돕습니다.

- 캡처링은?

반대로 최상위 태그에서 해당 태그를 찾아 내려간다.

예제

```jsx
const divs = document.querySelectorAll("div");

const clickEvent = (e) => {
  console.log(e.currentTarget.className);
};

divs.forEach((div) => {
  div.addEventListener("click", clickEvent, { capture: true });
});
```

`<div class="DIV3">DIV3</div>`를 클릭한다면 위에서부터 찾아 내려오기 때문에 콘솔에는 DIV1, DIV2, DIV3이 순서대로 찍힐 것이다.

이벤트 버블링은 일반적으로 이벤트 처리를 더 효율적으로 만들어줍니다. 여러 개의 하위 요소가 같은 이벤트를 처리해야 할 때, 이벤트 버블링을 통해 각 하위 요소에 개별적으로 이벤트 리스너를 추가하지 않아도 됩니다. 대신 이벤트를 상위 요소에 하나의 이벤트 리스너를 추가하면 해당 이벤트가 하위 요소에서 발생하면 상위 요소에서 처리됩니다.

메모리 관점에서도 이점이 있습니다. 이벤트 리스너가 많은 요소에 모두 개별적으로 리스너를 추가하는 것은 불필요한 메모리 사용을 초래할 수 있습니다. 이벤트 버블링을 활용하면 이벤트 처리를 최상위 요소에서 한 번만 추가하므로 메모리 사용을 절약할 수 있습니다.

# this와 연관지어서 bind에 대해서 설명해주세요.

<code>`this`</code>는 현재 실행 중인 함수나 메서드가 어떤 <code>`객체`</code>에 바인딩되어 있는지를 가리키는 특별한 <code>`키워드`</code>

1. <code>`call`</code> 메서드:`call` 메서드는 함수를 호출할 때 <code>`this`</code> 값을 설정합니다. 함수를 호출하는 동안 함수 내에서 <code>`this`</code>가 특정 객체를 가리키도록 지정할 수 있습니다. <code>`call`</code> 메서드는 첫 번째 매개변수로 설정하려는 <code>`this`</code> 값(객체)을 받고, 이어서 함수에 전달할 매개변수를 순서대로 받습니다.

   ```jsx
   function sayHello() {
     console.log(`Hello, ${this.name}!`);
   }

   const person = { name: "John" };
   sayHello.call(person); // "Hello, John!"
   ```

2. <code>`apply`</code> 메서드:`apply` 메서드는 `call`과 비슷하지만 매개변수를 배열로 받습니다. 함수에 전달할 매개변수가 배열에 담겨 있을 때 유용합니다.

   ```jsx
   function sayHello(greeting) {
     console.log(`${greeting}, ${this.name}!`);
   }

   const person = { name: "Alice" };
   sayHello.apply(person, ["Hi"]); // "Hi, Alice!"
   ```

3. `bind` 메서드:`bind` 메서드는 함수의 `this` 값을 영구적으로 설정합니다. `bind`를 사용하여 새로운 함수를 생성하며, 이 새로운 함수의 `this` 값은 원본 함수의 `this` 값을 변경하지 않습니다. 대신 새로운 함수가 호출될 때 항상 설정한 `this` 값을 가집니다.

   ```jsx
   function sayHello() {
     console.log(`Hello, ${this.name}!`);
   }

   const person = { name: "Sarah" };
   const greet = sayHello.bind(person);

   greet(); // "Hello, Sarah!"
   ```

이러한 메서드들을 사용하여 함수의 `this` 값을 조작할 수 있으므로, 함수가 다양한 객체와 함께 동작하도록 할 수 있습니다.

# 데이터 바인딩에 대해서 설명해주세요.

바인딩이란 화면상에 보여지는 데이터(View)와 브라우저 메모리에 있는 데이터(Model)을 묶어서(Binding) 서로 간의 데이터를 동기화하는 것이다. 예를 들어, HTML에서 서버 혹은 스크립트 상에서 받아온 데이터를 화면상에 그려주고 있다고 가정했을 때, 해당 값이 변경 될 경우 다시 HTML 상에서 데이터(값)를 변경된 값에 따라 맞춰주는 동작을 '데이터 바인딩'이라고 한다. 또한 부모 컴포넌트에서 자식 컴포넌트로는 Props를 통해 데이터를 전달하고, 자식 컴포넌트에서 부모 컴포넌트로는 Emit Event를 통해서 데이터를 전달하는 구조이다.

데이터 바인딩의 종류는 두가지가 있다.

첫번째는 Javascript(Model)에서 HTML(View)로 한 방향으로만 데이터를 동기화하는 단방향 바인딩이다. 대표적으로 React, Vue, Angular가 있다.

두번째는 Javascript와 HTML 사이에 View Model이 존재하여 하나로 묶여 둘 중 하나만 변경되어도 함께 변경되는 양방향 바인딩이다. 대표적으로 AngularJS가 있다.

# 자바스크립트의 메모리 관리에 대해 아는 대로 설명해주세요(\*)

자바스크립트의 메모리 관리는 런타임 환경에서 변수, 객체, 함수 등의 데이터를 할당하고 해제하는 프로세스를 관리하는 중요한 부분입니다.
자바스크립트는 동적 타입 언어로, 개발자가 명시적으로 메모리 할당 및 해제를 관리하지 않아도 됩니다. 대신 가비지 컬렉터가 더 이상 사용하지 않는 객체나 데이터를 자동으로 감지하고 해제합니다.
가비지 컬렉션이 수집을 못하게 되는 상황을 조심할 필요가 있습니다. 예를 들어 `순환 참조`라는 것이 있습니다.

```tsx
function createObjects() {
  let objA = {};
  let objB = {};

  // objA와 objB가 서로를 참조
  objA.refToB = objB;
  objB.refToA = objA;

  // 함수 종료 후, objA와 objB에 대한 참조가 사라짐
}

createObjects();
```

<code>createObjects</code> 함수는 두 개의 빈 객체 <code>objA</code>와 <code>objB</code>를 만들고 서로를 참조하도록 설정합니다.
함수가 종료된 후에는 함수 내부에서 생성된 objA와 objB에 대한 외부 참조가 없기 때문에, 이 두 객체는 논리적으로 사용되지 않지만, 순환 참조로 인해 가비지 컬렉터가 이들을 수집하지 못하게 되기 때문에 저러한 형식의 코드 작성은 피해야 합니다. -> 메모리 누수가 발생합니다.

# 메모리 누수를 위해 해야할 것들을 알려주세요.

- 사용하지 않는 객체에 대한 참조(reference)를 명시적으로 제거.
- 이벤트 리스너 등 자원 해제를 제대로 처리.
- 큰 데이터나 불필요한 데이터 구조를 빠르게 해제하고 재활용하지 않는 것.

# Promise와 Callback의 차이를 설명해주세요.

콜백함수는 비동기 로직의 결과값을 처리하기 위해 callback 안에서만 처리를 해야하고, 콜백 밖에서는 비동기에서 온 값을 알 수가 없다. 프로미스를 사용하면 비동기에서 온 값이 프로미스 객체에 저장되어서 코드 작성이 용이해진다. 즉, 콜백은 매번 비동기를 실행해야 그 값을 사용 가능해서 값을 저장하고 싶다면 프로미스를 사용하는 것이 낫다. 또한 프로젝트가 점점 복잡해지고 콜백함수가 중첩되면 가독성도 현저히 떨어지게 된다.(콜백 지옥)

# var, let, const의 차이를 설명해주세요.

변수란 하나의 값을 저장하기 위해 확보한 메모리 공간 자체 또는 그 메모리 공간을 식별하기 위해 붙인 이름을 말한다. 변수 선언에는 var, let, const가 있는데, let과 const가 나온 ES6 이전에는 var만 사용했는데 여기에는 세 가지 문제점이 존재한다.

- 변수 중복 선언 가능하여, 예기치 못한 값을 반환할 수 있다.
- 함수 레벨 스코프로 인해 함수 외부에서 선언한 변수는 모두 전역 변수로 된다.
- 변수 선언문 이전에 변수를 참조하면 언제나 undefined를 반환한다.

이에 해결책으로 제시된 let과 const의 특징은 다음과 같다.
let은 변수 중복 선언이 불가능하지만 재할당은 가능하다.
const는 let과 같이 재선언이 불가능하지만, 또 재할당도 불가능하다. 다만 원시 값만 불가능하고 객체는 가능하다. 또한 반드시 선언과 동시에 초기화를 진행해야한다.

```tsx
function testVarLetConst() {
  var a;
  let b;
  // const c;
  // 오류가 나옴. 할당 이전에 실행 불가, SyntaxError: Missing initializer in const declaration 에러가 나옴

  console.log({
    a,
    b,
  });
  // {a: undefined, b: undefined}
  a = "a";
  b = "b";
  console.log({
    a,
    b,
  });
  // {a: 'a', b: 'b'}
}

testVarLetConst();
```

### var, let의 차이점

- var로 선언된 변수는 함수 스코프(Function Scope)에서 호이스팅
- let으로 선언된 변수는 블록 스코프(Block Scope)에서 호이스팅, 블록 스코프란 중괄호 {} 내에서만 유효한 스코프

# Closure에 대해서 설명해주세요.

클로져란 어떤 함수가 다른 함수 내부에서 정의되고, 그 함수가 외부 함수의 변수에 접근할 수 있는 경우를 가리킨다.
이때, 외부 함수의 변수가 외부 함수의 스코프를 내부 함수에서 여전히 접근 가능하며, 이 내부 함수를 클로저라고 한다.
즉, 클로저는 주변 함수에 대한 참조와 함께 포함된 함수의 조합이다. -> 클로저는 내부 함수에서 외부 함수의 범위에 대한 접근을 제공한다.

클로저는 함수와 그 함수가 선언됐을 때의 렉시컬 환경의 조합입니다. 이 환경은 클로저가 생성된 시점의 유효 범위 내에 있는 모든 지역 변수로 구성된다.

```javascript
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  }; // 외부 변수 x를 사용할 수 있다.
  innerFunc();
}

outerFunc(); // 10
```

### 클로저의 특징

1. Javascript에서 클로저는 함수 생성 시 함수가 생성될 떄마다 생성된다.
2. 함수 내부에서 함수 정의: 클로저가 되기 위해서는 어떤 함수 내에서 내부 함수를 정의해야 한다.
3. 외부 변수 접근: 내부 함수는 외부 함수의 변수에 접근할 수 있다.
4. 외부 함수 종료 후에도 접근 가능: 외부 함수가 실행을 마치고 종료되더라도 클로저 내부 함수는 여전히 외부 변수에 접근할 수 있다. 이는 클로저가 자유 변수라고도 불리는 외부 변수를 갖고 있기 때문이다.

### 클로저의 사용

클로저는 주로 현재 상태를 기억하고 변경된 최신 상태를 유지하거나(1), 전역 변수의 사용을 억제(2)하고, 정보를 은닉(3)할 때 사용할 수 있다.

(참고 자료)[https://poiemaweb.com/js-closure#2-%ED%81%B4%EB%A1%9C%EC%A0%80%EC%9D%98-%ED%99%9C%EC%9A%A9]
(참고 자료2)[https://poiemaweb.com/js-closure]

### 클로저를 생각해야 하는 이유

자바스크립트는 쉬운 함수 중첩과 패턴으로 인하여 너무나도 쉽게 클로저가 생성된다. -> 이는 메모리 누수를 일으키므로 조심해서 개발을 진행해야 한다.

### 추가 자료

http://jibbering.com/faq/notes/closures/

# javascript 성능 최적화를 위해 노력한 것을 설명해주세요

1. 크롬 개발자 도구를 이용한 메모리 누수 찾기
   메모리 누수에는 크게 두 가지의 형태가 있다. 하나는 주기적으로 메모리 사용량이 증가하는 형태이고, 다른 하나는 단 한번만의 메모리 증가를 유발시키는 형태이다.
   일반적으로 전자를 탐지하는 것이 쉬운 편이다. 하지만 전자 유형의 누수는 메모리가 계속 늘어나면 브라우저가 느려지거나 스크립트 실행이 중지되어 성가신 일을 야기하기도 한다.
   후자 유형인 주기적이지 않은 누수는 다른 메모리 할당에 비해 눈의 뛸 정도로 큰 경우 쉽게 발견할 수 있다. 하지만 흔치 않기 때문에, 잘 인지못하고 넘어가는 경우가 많다.

<details>
  <summary>예제</summary>

```tsx
var x = [];

function createSomeNodes() {
  var div,
    i = 100,
    frag = document.createDocumentFragment();
  for (; i > 0; i--) {
    div = document.createElement("div");
    div.appendChild(
      document.createTextNode(i + " - " + new Date().toTimeString())
    );
    frag.appendChild(div);
  }
  document.getElementById("nodes").appendChild(frag);
}
function grow() {
  x.push(new Array(1000000).join("x"));
  createSomeNodes();
  setTimeout(grow, 1000);
}
```

<code>grow</code>가 호출되면 div 노드들을 만들고 DOM에 추가시킨다. 또한 큰 배열을 할당시키고 이를 글로벌 변수에 참조시킨다. 이 코드는 실행시 크롬 도구를 살펴보면 메모리가 꾸준히 증가하는 것을 확인해 볼 수 있다.

Performace 메뉴에서 memory 체크박스에 체크한 후 record 버튼을 누룬다. 예제의 THE BUTTON 버튼을 눌러 메모리 누수를 시켜본다. 그 후 record를 정지시킨 후 결과를 살펴본다.

이 사진에서 메모리 누수가 있다는 것을 보여주는 큰 징후가 두 개 있습니다. nodes(초록선) 와 JS heap(파란선) 그래프입니다. 노드들이 꾸준이 증가하며 감소되지 않습니다. 이것이 가장 큰 징후입니다.

JS heap 그래프도 역시 메모리 사용이 계속 증가되고 있음을 보여줍니다. 하지만 garbage collector의 영향으로 알아채기가 쉽지는 않습니다. 초기에 메모리가 증가하다가 한 번 크게 감소하고 더 증가하다가 또 감소하는 형태가 반복됨을 확인할 수 있습니다. 이 경우 핵심은, GC에 의해 메모리 사용량이 감소할 때마다 힙의 크기가 이전보다 더 크게 유지된다는 점입니다. 다시 말해서 GC가 많은 양의 메모리를 수집하는 데 성공하고 있지만, 그 중 일부가 주기적으로 누수되고 있다는 것입니다.

이제 메모리 누수가 있다는 것을 확신을 가질 수 있습니다. 이제 어디서 누수가 생기는지 찾아봅시다.

두 개의 스냅샷 찍기
어디서 메모리 누수가 생기는지 찾기 위해서 크롬 개발자 도구의 Memory 메뉴(구 Profiles)를 이용할 것입니다. 이번 단계를 수행하기 위해 위 단계에서 접속한 크롬 예제 페이지를 새로고침 합니다. 이제 Take Heap Snapshot 기능을 사용할 것 입니다.

페이지를 새로고침하고 페이지 로딩이 끝나면 heap 스냅샷을 생성합니다. 이 스냅샷을 기준으로 사용하겠습니다. 그런 다음 다시 The Button 을 누르고 몇 초간 기다린 후에 두번째 스냅샷을 생성합시다. 스냅샷을 생성했다면 코드에 중단점을 설정하여 더 이상 누수가 되지 않게 하는 것이 좋습니다.

두 스냅샷의 메모리 할당을 비교할 수 있는 두가지 방법이 있습니다. Summary 를 선택한 다음, 우측에 있는 All objects 를 Objects allocated between Snapshot1 and Snapshot 2 를 선택하거나 Summary 대신 Comparison 을 선택하면 됩니다. 두 방법 모두 두 스냅샷의 할당된 객체 목록이 표시됩니다.

이 예제에서 메모리 누수가 크기 때문에 매우 쉽게 발견됩니다. (string) Constructor의 Size Delta 표를 확인 해봅시다. 58개의 객체 생성으로 8MB를 차지하고 있습니다. 의심스로운 결과입니다. 새로운 객체들이 할당되었지만 해제되지 않아 8MB의 메모리가 소비되었습니다. (string) Constuctor 표를 열어보면 사이즈가 매우 큰 할당이 몇 개 있는 것을 확인하실 수 있습니다. 이 중 하나(xxxxxxxx… 로 표시된)를 선택하여 하단의 Retainers 표를 살펴보면 흥미로운 점을 찾을 수 있습니다.

선택한 할당이 배열의 일부임을 확인할 수 있습니다. 이 배열은 전역 window 객체의 x 변수로 참조되어 있다고 나옵니다. 이는 수집되지 않는 루트 (window) 객체에 큰 사이즈의 객체가 참조되어 있음을 우리에게 알려줍니다. 이제 잠재적인 메모리 누수와 그 위치를 발견했습니다.

꽤나 기쁜 발견이지만 우리의 예제는 매우 간단했습니다. 이 예제에서 볼 수 있는 것과 같이 큰 할당은 일반적인 경우는 아닙니다. 다행이도 우리의 예제는 작긴하지만 DOM 노드의 누수 문제도 포함하고 있었습니다. 위 스냅샷을 이용하여 이 노드들을 쉽게 찾을 수 있었지만, 규모가 큰 사이트에서는 더 복잡하여 찾기가 쉽지는 않을 것 입니다. 최신 버전의 크롬은 이런 작업에 적합한 추가적인 도구를 제공하는데, 바로 Record Heap Allocations 기능입니다.

https://itstory.tk/entry/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%88%84%EC%88%98%EC%9D%98-4%EA%B0%80%EC%A7%80-%ED%98%95%ED%83%9C

</details>

</details>

# TDZ란?

Temporal Dead Zone, 스코프의 시작 지점부터 초기화 시작 지점까지의 구간.
변수는 선언, 초기화, 할당 세 단계에 걸쳐 생성되는데, var는 선언과 초기화를 동시에 진행해서 TDZ가 따로 존재하지 않지만 let은 선언과 초기화가 분리되어 TDZ가 존재한다. 그래서 아래 코드들 같이 선언만 된 상태에서 호출하고 그 뒤에 할당했을 때, let과 var는 다른 결과를 나타낸다.

```tsx
var name1;
console.log(name1); // undefined
name1 = "Mike"; // name1은 선언과 초기화가 동시에 되어 호이스팅 되고 ‘Mike’(할당)는 호이스팅 되지 않는다.

console.log(name2); // ReferenceError
let name2 = "Michael"; // name2의 선언만 호이스팅 되고 초기화는 되지 않은 채로 호출된다.
```

# 이벤트 핸들러 vs 이벤트 리스너

- 이벤트 핸들러 (Event Handlers):

이벤트 핸들러는 HTML 요소에 직접 이벤트 처리 코드를 연결하는 방법입니다.
주로 HTML 요소의 속성으로 사용되며, 이벤트가 발생할 때 실행할 JavaScript 코드를 포함합니다.
예를 들어,

```javascript
<button onclick="myFunction()">Click me</button>
```

와 같이 HTML 요소에 직접 이벤트 핸들러를 추가할 수 있습니다.
이벤트 핸들러는 요소와 이벤트 간의 강력한 결합을 가지며, 여러 이벤트 핸들러를 동시에 연결하기 어려울 수 있습니다.

- 이벤트 리스너 (Event Listeners):

이벤트 리스너는 HTML 요소와 JavaScript 코드를 분리하는 방법입니다.
addEventListener 메서드를 사용하여 HTML 요소에 이벤트 리스너를 추가합니다.
이벤트 리스너는 이벤트가 발생했을 때 실행할 함수를 지정하는 방식으로 작동합니다.
이벤트 리스너를 사용하면 하나의 요소에 여러 이벤트 리스너를 연결하고, 이벤트 핸들링 로직을 모듈화할 수 있으며, 요소와 이벤트 간의 결합이 느슨합니다.
예를 들어:

```javascript
const button = document.querySelector("button");
button.addEventListener("click", myFunction);
```

# Javascript는 어떤 언어입니까?

JavaScript는 `싱글 스레드`이면서 `블록킹` 언어입니다. `자바스크립트`는 `비동기 처리(정확히 말하면 비동기 처리 + 논블로킹)`를 통해 싱글 스레드이지만 블록킹 되지 않게 합니다. 하나의 요청이 완료될 때까지 기다리지 않고 동시에 다른 작업을 수행함으로써 문제를 해결합니다. 멀티 스레드가 아닌 이유는 동시성 문제(동시에 공유된 자원에 접근하는 경우)를 해결하기 까다롭기 때문입니다.

싱글 스레드 -스레드가 하나밖에 존재하지 않아 한번에 하나의 작업만 할 수 있습니다.
논 블록킹 - A함수가 B함수를 호출해도 제어권은 그대로 자신이 가지고 있는다.
비동기 처리 - 특정 로직의 실행이 끝날때까지 기다려주지 않고 나머지 코드를 먼저 실행

또한 자바스크립트는 동적 타입 언어입니다. JavaScript는 변수의 데이터 타입을 선언하지 않고, 런타임 시에 변수가 어떤 타입을 가질지 결정합니다. 이것은 변수에 어떤 종류의 데이터든 할당할 수 있음을 의미합니다. 따라서 변수의 타입은 런타임에 결정되며, 이는 런타임 동안 변수의 동작을 결정하는 중요한 특징입니다.

# esModule과 CommonJS (CJS)에 대해서 설명해주세요.

`esModule`과 `CommonJS (CJS)`는 JavaScript 모듈 시스템의 두 가지 주요 형식 중 하나입니다. 각각의 특징과 차이점은 다음과 같습니다:

1. 모듈 타입:
   - `ES Modules (ESM)`: ECMAScript 표준에 따라 구현된 자바스크립트 모듈 형식입니다. `import` 및 `export` 문을 사용하여 모듈을 가져오고 내보낼 수 있습니다. 브라우저와 Node.js 모두 ESM을 지원하며, `.mjs` 확장자 또는 `"type": "module"` 옵션을 사용하여 파일을 ESM으로 지정할 수 있습니다.
   - `CommonJS (CJS)`: Node.js에서 주로 사용되는 모듈 형식입니다. `require` 및 `module.exports`를 사용하여 모듈을 가져오고 내보냅니다.
2. 동기 vs. 비동기:
   - `ESM`: ESM은 기본적으로 비동기적으로 모듈을 가져오며, `import`문은 비동기 처리를 수행합니다. 이것은 브라우저에서도 동일하게 적용됩니다.
   - `CJS`: CJS는 동기적으로 모듈을 가져오는 경향이 있으며, `require` 문은 동기적으로 실행됩니다. Node.js에서는 기본적으로 동기식입니다.
3. 정적 vs. 동적:
   - `ESM`: ESM은 정적 모듈 시스템입니다. 즉, 모듈 가져오기 및 내보내기는 코드의 맨 위에 있어야 하며, 런타임 동안 조건부 또는 동적으로 수행할 수 없습니다. (무조건 상단에 import를 실행시켜야 한다.)
   - `CJS`: CJS는 동적으로 모듈을 가져오고 내보내는 것이 가능합니다. 이는 런타임 조건에 따라 다른 모듈을 동적으로 로드해야 하는 경우에 유용합니다. (어느 지점에서도 required 가능하다.)
4. Named vs. Default Export:
   - `ESM`: ESM은 명시적으로 명명된 내보내기 (named exports)를 지원합니다. 여러 개의 내보내기를 가질 수 있으며, `import { name } from 'module'`과 같이 가져옵니다.
   - `CJS`: CJS는 주로 `module.exports`를 사용하여 모듈을 내보내므로 주로 하나의 기본 내보내기 (default export)만을 가집니다. 이는 `const module = require('module')`과 같이 가져옵니다.
5. 사용 사례:
   - `ESM`: 브라우저에서 모듈을 사용하거나 최신 Node.js 버전에서 모듈을 사용하는 경우에 적합합니다. 또한 정적인 모듈 시스템이 필요한 경우에 유용합니다.
   - `CJS`: 오래된 Node.js 버전과의 호환성이 필요한 경우, 또는 동적 모듈 로딩이 필요한 경우에 적합합니다. Node.js 환경에서 널리 사용됩니다.

이러한 차이점을 고려하여 프로젝트의 요구 사항에 따라 `ES Modules` 또는 `CommonJS` 중 하나를 선택할 수 있습니다. Node.js의 최신 버전에서는 두 가지 모듈 형식을 모두 지원하므로 적절한 모듈 시스템을 선택할 수 있습니다.

### import와 require의 차이

```javascript
/* CommonJS */
const name = require("./module.js");

/* ES6 */
import name from "./module.js";
```

- require은 어느 지점에서나 호출할 수 있지만 import는 파일의 시작 부분에서 실행된다.
- 하나의 프로그램에서 두 키워드를 동시에 사용할 수 없다.
- 일반적으로 import는 사용자가 필요한 모듈 부분만 선택하고 로드 할 수 있어 더 선호된다. 또한 require보다 성능이 우수하면 메모리를 절약한다.

# 불변성을 유지하는 방법은?

불변성이란, 객체가 생성된 이후 그 상태를 변경할 수 없는 것. 객체 프로퍼티를 변경할 수 없는 상태.

- Spread Operator(ES6)를 사용해서 모든 property 복사

```jsx
const obj = { a: 1, b: 2 };
const newObj = { ...obj, c: 3 };
console.log(newObj); // { a: 1, b: 2, c: 3 }
```

- Object.assign을 사용해서 모든 property 복사

```jsx
const obj = { a: 1, b: 2 };
const newObj = Object.assign({}, obj, { c: 3 });
console.log(newObj); // { a: 1, b: 2, c: 3 }
```

- immer의 produce()를 사용해서 모든 property 복사

```jsx
import produce from "immer";

const obj = { a: 1, b: 2 };
const newObj = produce(obj, (draft) => {
  draft.c = 3;
});
console.log(newObj); // { a: 1, b: 2, c: 3 }
```

추가로, depth가 깊어지면 Spread Operator와 Object.assign은 코드가 많아져 immer의 사용이 더 유리하다.

# 상속 및 prototype에 대해서 설명해주세요.

JavaScript는 프로토타입 기반의 언어로, 모든 객체는 프로토타입 객체와 연결되어 있다.
여기서 프로토타입은 객체가 다른 객체로부터 속성과 메소드를 상속받을 수 있도록 하는 링크의 역할을 한다. -> 상속이 가능하게 한다.

```javascript
const car = {
  wheels: 4,
  drive() {
    console.log("drive..");
  },
},

const bmw = {
  color: 'red',
  navigation: 1,
}

bmw.__proto__ = car;
```

- Prototype Chain
  계속해서 상속이 가능하다.
  ![스크린샷 2023-09-27 오후 7 52 34](https://github.com/in-ch/tech-inverview-study/assets/49556566/440f6dcf-2fb1-496c-95a8-4d34a189a8f1)

- 상속받은 객체의 프로퍼티 순회

1. for in
   <code>for(p in x)</code> 이렇게 사용하고 상속받은 key 값들도 다 출력한다.

2. Object.keys, Object.values

- 상속받은 key값이나 value 값들은 출력하지 않음.

- 생성자 함수에서의 상속 방법
  <code>**proto**</code>를 사용하는 것이 아니라 <code>prototype</code>을 사용한다.

```javascript
// const car = {
//   wheels: 4,
//   drive() {
//     console.log('drive...');
//   }
// }

const Bmw = function (color) {
  this.color = color;
};

Bmw.prototype.wheels = 4;
Bmw.prototype.drive = function () {
  console.log("drive");
};

const x5 = new Bmw("red");
const z4 = new Bmw("blue");

// x5.__proto__ = car;
// z4.__proto__ = car;
```

# 렉시컬 스코프에 대해서 설명해주세요.

자바스크립트에서 렉시컬 스코프(lexical scope)는 변수 범위(스코프)를 결정하는 규칙을 의미한다.
이 규칙에 따라 변수가 어디에서 유효한지를 결정하게 된다. -> 렉시컬 스코프는 코드를 작성할 때 정적으로 결정되며, 실행 시간에 동적으로 변경되지 않는다.

렉시컬 스코프의 주요 특징 및 동작 원리는 다음과 같다.

1. 함수 범위: 함수 내에서 선언된 변수는 해당 함수의 렉시컬 스코프에 속합니다. 이것은 함수 내부에서 선언된 변수가 외부 스코프에서는 보이지 않음을 의미합니다.
2. 중첩 스코프: 렉시컬 스코프는 중첩된 함수에서도 작동합니다. 내부 함수는 외부 함수의 스코프에 접근할 수 있지만 외부 함수는 내부 함수의 스코프에 접근할 수 없습니다.
3. 전역 스코프: 전역 스코프는 모든 함수의 외부에 존재하는 스코프로, 전역 변수는 어디서든 참조할 수 있습니다.
4. 함수 스코프 체인: 렉시컬 스코프는 함수의 중첩과 스코프 체인을 형성합니다. 함수가 변수를 찾을 때, 먼저 자신의 스코프에서 검색하고, 그 다음으로 외부 함수의 스코프를 찾습니다. 이런 식으로 계속 상위 스코프를 찾아나가는 구조입니다.

# 자바스크립트의 동작 원리에 대해서 설명해주세요.

![이벤트_루프.gif](https://github.com/Esoolgnah/Frontend-Interview-Questions/blob/main/Images/important-4/javascript-eventloop.gif)

1. 실행 컨텍스트가 실행되고 해당 작업은 <code>콜스택(Call Stack)</code>에 쌓이게 됩니다.
   여기서 콜스택은 실행된 코드의 환경을 저장하는 자료구조로, 함수 호출 시 이곳에 저장됩니다. LIFO 방식입니다.
2. 시간이 소요되는 작업들은 (예를 들어 setTimeout, 이벤트, HTTP 요청 메서드, 비동기 함수 등)은 WebAPI에서 대기하다가 WebAPI에 의해 콜백큐(Callback Queue)로 보내집니다. -> FIFO 방식
3. Call Stack이 비워져 있을때만 Callback Queue에 저장되어 있던 작업들이 Call Stack으로 보내지는데 이러한 역할을 수행하는 것을 <code>이벤트 루프</code>라고 합니다.

# javascript의 반복문의 종류에 대해서 설명해주세요.

1. for문
   일반적인 for문

2. forEach 문
   오로지 배열에서만 사용 가능하다. (map, set 에서도 지원)
   배열의 요소들을 반복하여 작업을 수행할 수 있다.
   forEach 구문의 인자로 callback 함수를 등록할 수 있고, 배열의 각 요소들이 반복될 때 이 callback 함수가 호출된다. -> index도 인자에 포함이 되어서 인덱스 값에도 접근이 가능하다.

   ```javascript
   var items = ["item1", "item2", "item3"];

   items.forEach(function (item, index) {
     console.log(item, index);
   });
   ```

3. for ... in 반복문
   for in 반복문은 객체의 속성들을 반복하여 작업을 수행할 수 있다. (모든 객체에서 사용이 가능하다.)
   다만 key값에만 접근이 가능하고 value 값에 접근하는 방법은 제공하지 않는다.
   왜냐하면 자바스크립트에서 객체 속성들은 내부적으로 사용하는 숨겨진 속성들을 가지고 있다. 그 중 하나가 [[Enumerable]]이며, for in 구문은 이 값이 true로 셋팅되어 속성들만 반복할 수 있다.
   value를 쓰고 싶다면 다음과 같이 해야 한다.

   ```javascript
   var obj = {
     a: 1,
     b: 2,
     c: 3,
   };

   for (var prop in obj) {
     console.log(prop, obj[prop]); // a 1, b 2, c 3
   }
   ```

   혹은 Object.keys, Object.values, Object.entries를 사용 가능하다.

   ```javascript
   const myObject = { a: 1, b: 2, c: 3 };
   const keys = Object.keys(myObject);
   const values = Object.values(myObject);
   const entries = Object.entries(myObject);

   for (const key of keys) {
     console.log(key, myObject[key]);
   }

   for (const value of values) {
     console.log(value);
   }

   for (const [key, value] of entries) {
     console.log(key, value);
   }
   ```

4. for .. of 반복문
   for .. of 구문을 사용하기 위해선 컬렉션 객체가 [Symbol.iterator] 속성을 가지고 있어야만 한다.
   컬렉션 객체란 데이터를 저장하고 관리하기 위한 다양한 자료 구조를 제공하는 객체나 클래스를 가르킨다. (배열, 객체, 맵, 셋 등)

   ```javascript
   var iterable = [10, 20, 30];

   for (var value of iterable) {
     console.log(value); // 10, 20, 30
   }
   ```

5. 기타
   map, reduce 등 등

### for in 반복문과 for of 반복문의 차이점

- for in 반복문: 객체의 모든 열거 가능한 속성에 대해 반복
- for of 반복문: [Symbol.iterator] 속성을 가지는 컬렉션 전용

# Map과 Set에 대해서 설명해주세요.

JavaScript에서 Map과 Set은 둘 다 데이터를 저장하는 데 사용되는 자료 구조이다.

### Set:

- `Set`은 중복을 허용하지 않는 값들의 컬렉션입니다.
- 유일한 값을 저장할 때 유용합니다. 예를 들어, 중복된 값을 제거하고 싶을 때 사용할 수 있습니다.
- `Set`은 값을 추가하거나 삭제할 수 있지만, 값을 기준으로 저장하며, 특정 키-값 쌍을 관리하지는 않습니다.

```javascript
let uniqueNumbers = new Set();
uniqueNumbers.add(1);
uniqueNumbers.add(2);
uniqueNumbers.add(1); // 중복된 값은 무시됨

console.log(uniqueNumbers); // 출력: Set { 1, 2 }
```

### Map:

- `Map`은 키-값 쌍을 저장하는 컬렉션입니다.
- 특정 키에 연결된 값을 찾을 때 유용합니다. 예를 들어, 객체와 비슷하지만 객체의 키로 문자열과 심볼만 사용할 수 있는 반면, `Map`은 어떠한 값도 키로 사용할 수 있습니다.
- 순서를 유지하므로 원하는 순서대로 요소를 반복할 수 있습니다.

```javascript
let userRoles = new Map();
userRoles.set("john", "admin");
userRoles.set("jane", "moderator");

console.log(userRoles.get("john")); // 출력: admin
console.log(userRoles.has("jane")); // 출력: true
```

### 어느 경우에 써야 할까?

- Set을 사용해야 하는 경우:
  - 중복된 값을 허용하지 않고 유일한 값들의 컬렉션을 유지하려 할 때
  - 값들의 순서가 중요하지 않은 경우
- Map을 사용해야 하는 경우:

  - 특정 키에 연결된 값을 저장하고 이를 검색하고 싶을 때
  - 데이터를 삽입한 순서를 유지하고 싶은 경우 (물론 es6부터는 obj도 순서를 보장하나, 좀 더 명시적으로 순서를 보장하기 위해서 map이 사용된다. )
  - 객체 키 이외의 값을 키로 사용하고 싶은 경우 (obj는 심볼과 문자열만이 키값으로 사용할 수 있다. map은 boolean 값도 키값으로 사용할 수 있다. )
    ex)

    ```javascript
    // 심볼 생성
    const mySymbol = Symbol("description");

    // 객체 생성 및 심볼을 속성의 키로 사용
    let obj = {
      name: "John",
      [mySymbol]: "This is a symbol property",
    };

    console.log(obj.name); // 출력: John
    console.log(obj[mySymbol]); // 출력: This is a symbol property
    ```

    여기서 심볼이란 JavaScript의 원시 데이터 타입 중 하나로, 유일성이 보장된 값을 생성할 때 사용된다.

# 배럴(Barrel) 파일이란?

배럴 파일은 주로 모듈 구조를 정리하고 효율적으로 관리하기 위한 디자인 패턴이다

여러 개의 컴포넌트, 서비스, 또는 모듈을 한 번에 가져오고 내보내고 싶은 경우, 이러한 파일을 만들어 모듈들을 그룹화할 수 있다. (다른 파일에서 내보내기를 다시 내보내는 파일이라고 이해할 수 있다.)

ex)

```jsx
export * from "./component1.component";
export * from "./component2.component";
export * from "./service1.service";
export * from "./service2.service";
```

이렇게 하면

```jsx
import {
  Component1Component,
  Component2Component,
  Service1Service,
  Service2Service,
} from "./barrel.module";
```

이런 식으로 한 파일에서 볼 수가 있다.

### 장점

프로젝트 구조를 정리하고 관리하기 쉽게 만들어준다. 특히 프로젝트가 커질수록 여러 모듈과 컴포넌트를 효율적으로 다루기 위한 방법 중 하나이다.

### 단점

1. 불필요한 모듈 불러오기

   인덱스 파일에서는 모든 모듈을 내보내기 때문에, 필요한 모듈만 불러오는 것이 어려울 수 있다.

   이로 인해 불필요한 코드가 번들에 포함될 수 있다.

2. 모듈 간의 의존성 오류

   여러 모듈이 상호 의존성을 가지고 있을 때, 인덱스 파일에서 모듈을 순환 참조할 수 있다. → 의존성 오류를 발생시킬 수 있다.

# 바벨 vs 폴리필

바벨(Babel)과 폴리필(polyfill)은 둘 다 JavaScript 코드를 브라우저 호환성을 갖춘 코드로 변환하거나, 브라우저에서 지원하지 않는 기능을 사용할 수 있게 만들어주는 도구이지만, 각각의 역할과 사용 시기에 차이가 있습니다.

### 1. 바벨 (Babel):

- 역할: 바벨은 주로 최신 ECMAScript 표준에 도입된 JavaScript 문법을 오래된 브라우저에서도 동작할 수 있는 호환성 있는 코드로 변환해주는 도구입니다. 이는 주로 트랜스파일러(transpiler)로서 동작하며, 새로운 ECMAScript 문법을 이전 버전의 ECMAScript 문법으로 변환합니다.
- 예시: ES6 (ECMAScript 2015)의 화살표 함수, 템플릿 리터럴, 클래스 등과 같은 문법을 ES5로 변환하여 오래된 브라우저에서도 실행 가능하게 만들어줍니다.

### 2. 폴리필 (Polyfill):

- 역할: 폴리필은 브라우저가 지원하지 않는 JavaScript 기능을 구현한 코드로, 이를 웹 애플리케이션에 포함시켜 브라우저 호환성을 확보하는 데 사용됩니다. 폴리필은 브라우저가 원래 지원하지 않는 메서드나 객체, 프로퍼티 등을 JavaScript로 직접 구현한 코드입니다.
- 예시: `Array.prototype.includes()`, `Promise`, `Object.assign()`과 같은 기능은 일부 오래된 브라우저에서 지원하지 않을 수 있습니다. 이런 경우에 해당 기능을 구현한 폴리필을 사용하여 호환성을 확보할 수 있습니다.

요약하면, 바벨은 JavaScript 문법을 호환성 있는 문법으로 변환하는 데 사용되고, 폴리필은 JavaScript API나 메서드를 구현하여 브라우저 호환성을 확보하는 데 사용됩니다. 일반적으로 프로젝트에서는 둘 다 함께 사용하여 최신 문법을 사용하고, 필요한 경우에만 폴리필을 추가하여 브라우저 호환성을 확보합니다.

# 즉시실행함수(IIFE)에 대해 설명해주세요.

IIFE (Immediately Invoked Funtion Expression)

즉시 실행 함수는 정의되자마자 즉시 실행되는 함수를 말한다. 다음과 같이 소괄호로 함수를 감싸서 문법을 사용한다.
함수 표현식을 정의하고 즉시 호출하는 것을 의미한다. 이것은 코드를 더 모듈화하고 스코프를 제어하는 데 도움이 된다.

```jsx
(function () {
  console.log("IIFE");
})();

// 화살표 함수
(() => {
  console.log("IIFE");
})();
```

즉시실행함수는 한 번의 실행 이후 없어지기 때문에 단 한번의 사용한 필요한 함수에 사용된다. 대표적인 예시로는 변수를 초기화 하는 함수가 있다. 또 자바스크립트 모듈을 만들때에도 즉시 실행함수가 많이 활용된다.

### 즉시실행함수를 왜 사용하는가?

1. 필요없는 전역 변수의 생성을 줄일 수 있다.

함수를 생성하면 그 함수는 전역 변수로써 남아있게 되고, 많은 변수의 생성은 전역 스코프를 오염시킬 수 있다. 즉시실행함수를 선언하면 내부 변수가 전역으로 저장되지 않기 때문에 전역 스코프의 오염을 줄일 수 있다.

2. private한 변수를 만들 수 있다.

즉시실행함수는 외부에서 접근 할 수 없는 자체적인 스코프를 가지게된다. 이는 클로저의 사용 목적과도 비슷하며 내부 변수를 외부로부터 private하게 보호 할 수 있다는 장점이 있다.

3.  IIFE를 사용하면 전역 스코프를 오염시키지 않고 코드를 작성할 수 있으므로 다른 라이브러리와 충돌하지 않는다.

4.  IIFE를 사용하여 모듈화된 코드를 작성할 수 있으며, 모듈 간의 충돌을 방지하고 코드를 정리하는 데 도움이 된다.

# 자바스크립트의 배열이 실제 자료구조 배열이 아닌데 그 이유는?

자바스크립트의 배열은 실제 자료구조의 배열과 다르게 HashMap으로 구현되어있다. 이 HashMap을 구현하기 위해서는 연결리스트로 구현하게 되는데 연결리스트에서 값을 찾기 위해서는 탐색해나가면서 값을 찾는 불상사가 발생한다. 이를 해결하기 위해서 타이핑된배열(Int8Array,Float32Array 등) 이 추가되고 있다.

![KakaoTalk_Photo_2023-09-14-21-46-14](https://github.com/in-ch/tech-inverview-study/assets/49556566/642af43b-94bb-4f0f-9a10-d480221c9df2)
Map은 키와 값으로 구성된 Entry객체를 저장하는 구조를 가지고 있는 자료구조

![KakaoTalk_Photo_2023-09-14-21-46-20](https://github.com/in-ch/tech-inverview-study/assets/49556566/1825a103-9d98-470d-99ac-b572a174d0c0)
연결리스트(Linked List)는 각 노드가 데이터와 포인터를 가지고 한 줄로 연결되어 있는 방식으로 데이터를 저장하는 자료구조

### 여기서 타이핑된 배열이란 뭘까?

기존 JavaScript 배열과는 다르게 메모리의 연속적인 영역에 이진 데이터를 저장한다.
Int8Array: 8비트(1바이트) 부호 있는 정수 배열로, 바이너리 데이터를 저장할 때 사용됩니다. 각 요소는 -128에서 127 사이의 값을 나타낼 수 있습니다.

Uint8Array: 8비트 부호 없는 정수 배열로, 바이너리 데이터를 저장할 때 사용됩니다. 각 요소는 0에서 255 사이의 값을 나타낼 수 있습니다.

Int16Array 및 Uint16Array: 각각 16비트 부호 있는 및 부호 없는 정수 배열로, 두 번째 바이트 단위의 데이터를 저장할 때 사용됩니다.

Int32Array 및 Uint32Array: 각각 32비트 부호 있는 및 부호 없는 정수 배열로, 네 번째 바이트 단위의 데이터를 저장할 때 사용됩니다.

Float32Array 및 Float64Array: 각각 32비트 및 64비트 부동 소수점 수 배열로, 부동 소수점 수를 저장할 때 사용됩니다.

### 타이핑된 배열(Typed Arrays)이 일반 배열보다 더 빠른 이유

고정 크기와 타입: 타이핑된 배열은 고정 크기를 가지며, 각 요소의 데이터 타입이 미리 정의되어 있습니다. 이로 인해 메모리 할당과 데이터 처리가 더 효율적으로 이루어집니다. 예를 들어, 정수 배열에서 각 요소는 정확히 1바이트 또는 4바이트로 정의되어 있으므로 데이터 유형 변환이 필요하지 않습니다.

연속적인 메모리 할당: 타이핑된 배열은 연속적인 메모리 영역에 데이터를 저장하므로 CPU 캐시의 이점을 활용하기 쉽습니다. 이렇게 하면 데이터에 더 빠르게 액세스할 수 있습니다.

저수준 메모리 제어: 타이핑된 배열은 JavaScript의 가비지 컬렉션과 같은 런타임 오버헤드가 적습니다. 이는 메모리 관리와 정리에 대한 제어를 개발자에게 제공하므로 메모리 관리를 더 효율적으로 수행할 수 있습니다.

원시 데이터 형식 지원: 타이핑된 배열은 데이터를 그대로 메모리에 저장하므로 데이터를 직접 처리할 수 있으며, 특히 원시 데이터 유형(예: 정수 또는 부동 소수점 수)을 사용하여 수치 계산 및 비트 조작을 수행할 때 더 빠릅니다.

배열 형식 변환이 필요하지 않음: 타이핑된 배열은 데이터 유형을 미리 지정하므로 배열 요소에 액세스할 때 형식 변환이 필요하지 않습니다.

이러한 이유로 타이핑된 배열은 특정 작업, 특히 바이너리 데이터 처리 및 수치 계산에 대한 성능을 향상시키는 데 더 적합합니다.

# any, unknown, never

<code>unknown</code> 은 타입스크립트의 Top-Type 이다.
따라서 타입스크립트에 존재하는 모든 타입을 할당 할 수 있다.
다시 말해서, 모든 타입의 공통적인 연산밖에 할 수 없다.

```typescript
let foo: unknown = "bar";

foo = 10; // ok
foo = ["hello", "world"]; // ok

foo[0]; // Error
foo(); // Error
foo.bar; // Error

let bar: boolean = foo; // Error !!
// foo 변수의 타입이 unknown 이기 때문에 boolean 타입의 변수에 할당 할 수 없다.

// 할당하기 위해서는 아래와 같이 타입을 명시해줘야 한다.
let bar: boolean = foo as boolean;
```

<code>any</code>는 타입 검사를 항상 만족한다.
따라서, 모든 타입의 연산을 할 수 있다.
이에 따라 의도치 않은 사이드 이펙트가 있을 수 있다.
예를들어 객체에 존재하지 않는 프로퍼티에 접근해도 컴파일러는 아무런 에러를 띄워주지 않는다. (런타임에 문제가 된다)

```typescript
let foo: any = "bar";

foo.bar; // 컴파일러가 뭐가 잘못된지 모른다.
foo(); // 컴파일러가 뭐가 잘못된지 모른다.
```

<code>never</code>타입은 모든 타입의 하위 타입이다.
그래서 그 어떤 값도 Never 타입에 할당 할 수 없다.

```typescript
let foo: never = "bar"; // Error
let bar: never = 10; // Error
```

ex) 함수가 아무것도 반환하지 않을 때 -> never 를 반환타입으로 지정하여 타입추론 예외를 제거한다.

```typescript
function throwError(errorMsg: string): never {
  throw new Error(errorMsg);
}
```

반환 타입을 void 로 했을 때와 차이점은 void는 null 혹은 undefined 값의 반환을 허용한다는 것이고 never는 그렇지 않다는 것이다.

ex) 특정 타입 값을 할당받지 못하게 할 때

```typescript
type NonString<T> = T extends string ? never : T;
```

# 구조분해할당(Destructuring)이란?

객체나 배열에 저장된 데이터를 전체가 아닌 일부만 사용하고 싶을 때, 이들을 변수로 '분해’할 수 있게 해주는 문법이다. ES2015에 새롭게 나온 기술로, 기본적인 구조는 아래와 같다.

```jsx
let arr = ["Bora", "Lee"];

let [firstName, surname] = arr;

[macbook, ipad] = [ipad, macbook]; // macbook과 ipad 값 교환
```

위 그림처럼, 배열의 분해는 인덱스를 기준으로 변수에 하나씩 할당되며 할당되는 배열이나 할당 받는 변수들들의 갯수가 달라도 사용이 가능하다.

또한 두 변수의 값을 교환할 때도 유용하다.

```jsx
let options = {
  title: "Menu",
  width: 100,
  height: 200,
};

let { title, ...size } = options;
```

위 그림처럼, 객체의 분해는 프로퍼티 네임을 기준으로 할당되어서 인덱스와 상관없이 할당된다.

# 콜 스택과 메모리 힙이란?

자바스크립트 엔진은 `Memory Heap` 과 `Call Stack` 으로 구성되어 있습니다. 가장 유명한 것이 구글의 V8 Engine입니다. 자바스크립트는 단일 스레드 (single thread) 프로그래밍 언어인데, 이 의미는 `Call Stack이 하나` 라는 이야기입니다. 즉 멀티가 되지 않고, 하나씩 하나씩 처리한다는 의미입니다.

### 콜 스택(Call Stack)

원시 타입(숫자 등) 데이터가 저장된다.

실행 콘텍스트(Execution Context)를 통해

1. 변수 식별자(이름) 저장,

2. 스코프 체인 및 this 관리,

3. 코드 실행 순서 관리 등을 수행.

### 메모리 힙(Memory Heap)

참조 타입(배열, 객체, 함수 등) 데이터가 저장된다.

메모리 할당이 일어나는 곳.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwyILC%2Fbtrdon3nQV9%2FyWgZ1qDmEZDwzINEm5dkf1%2Fimg.png" width= 800 />

# ^ 마크에 대해서 설명해주세요.

`package.json` 파일의 `dependencies` 섹션에 `^` (caret) 기호를 사용하면 해당 패키지의 마이너 버전이나 패치 버전까지는 업데이트를 허용하는 의미

예를 들어, `"^asdf: 0.0.1"`으로 지정된 경우에는 실제로는 `0.0.x` (어떤 x 값이든) 버전을 다운로드하게 된다.
만약 0.0.3 버전이 나오면, 최신 버전인 0.0.3 버전을 가져온다.

버전 넘버링 규칙은 SemVer(시멘틱 버전) 규칙을 따르며, 메이저 버전, 마이너 버전, 패치 버전으로 구성되어 있다.

caret (`^`) 기호는 메이저 버전이 동일하면 마이너 버전 및 패치 버전을 업데이트하는 것을 허용한다.

만약 그게 싫다면 <code>yarn install --frozen-lockfile</code>를 사용해서 패키지 버전을 고정시켜야 한다.

# 컨벤션들

[컨벤션들 링크](https://ui.toast.com/fe-guide/ko_CODING-CONVENTION)

# async의 올바른 위치

```javascript
// 1) 함수 선언식
async function example1(a, b) {
  return a + b;
}

// 2-1) 함수 표현식
const example2_1 = async function add(a, b) {
  return a + b;
};

// 2-2) 함수 표현식(Anonymous)
const example2_2 = async function (a, b) {
  return a + b;
};

// 3-1) 화살표 함수
const example3_1 = async (a, b) => {
  return a + b;
};

// 3-2) 화살표 함수(shortened)
const example3_2 = async (a, b) => a + b;
```

# HTML과 JSX의 차이

1. 표현식을 사용할 땐 중괄호 사용, 삼항연산자도 값으로 평가된다.
2. 속성명은 카멜케이스, 자바스크립트 예약어와 같은 속성명은 다르게 작성한다. (for => htmlFor, class => className)
3. JSX는 동적인 마크업이다.
4. 2줄 이상의 JSX는 하나의 태그로 감싸야한다.(Fragment도 가능).

# truthy & falsy란?

**falsy**는 조건문 내부에서 false로 취급되는 값.

- false
- 0, -0, 0n, 0x0n
- NaN
- ‘’, “”, ``
- null
- undefined

**truthy**는 조건문 내부에서 true로 취급되는 값. 위의 falsy값들을 제외하면 모두 true로 취급된다. 참고로 {}, []처럼 빈 객체나 배열도 true 값이다.

# 타입 가드에 대해서 설명

TypeScript에서 Type Guard는 변수의 타입을 좁혀나가는 역할을 하는 특별한 코드 패턴
이를 통해 코드에서 타입 안정성을 확보하고 다양한 상황에 따라 정확한 타입을 사용할 수 있다.

Type Guard를 사용하는 가장 일반적인 상황은 유니온 타입을 다룰 때이다.
예를 들어 string | number와 같은 유니온 타입이 있는 경우, 특정 조건을 충족할 때마다 해당 변수의 타입을 더 좁혀서 더 구체적인 타입을 사용할 수 있다.

가장 흔한 Type Guard는 `typeof`, `instanceof`, `in` 등의 연산자를 사용하는 것이다.

## 기명 함수 (Named Function Expression)

`기명 함수`란 이름 있는 함수이며,

함수 표현식으로 만든 함수에 이름을 붙여주면 `기명 함수 표현식`이라고 한다.

함수 표현식으로 함수가 할당된 변수에는 자동으로 'name'이라는 프로퍼티를 가지게 된다.

이 때, 함수에 이름을 정해주지 않으면 변수 이름 자체를 값으로 가지고,

```jsx
const sayHi = function () {
  // 이름을 정해주지 않음
  console.log("Hi");
};
console.log(sayHi.name); // sayHi (변수명이 프로퍼티 value)
```

함수에 이름을 붙여주면 함수 이름을 값으로 갖는다.

하지만 이 함수 이름으로 함수 외부에서 **함수를 호출할 때는 사용할 수 없다.**

```jsx
const sayHi = function printHi() {
  // 이름 정해줌
  console.log("Hi");
};
console.log(sayHi.name); // printHi
sayHi(); // Hi
printHi(); // ReferenceError (함수 이름으로 함수를 호출할 수 없다)
```

대신 기명 함수 표현식에서 이 `함수 이름`으로는 함수 내부에서 `함수 자체`를 가리킬 때 사용할 수 있다.

# 프로미스 객체가 생성되면 발생하는 일

프라미스 생성자가 만들어지면 다음과 같은 일이 일어난다.

1. 프라미스 객체가 생성된다.

1. 생성된 내부 값: `[[PromiseState]], [[PromiseResult]], [[PromiseIsHandled]],[[PromiseFulfillReactions]]` 그리고 `[[PromiseRejectReactions]]`

   1. **PromiseState (프로미스 상태)**:
      - **`PromiseState`**는 **`Promise`** 객체의 상태를 나타내는 속성
      - 세 가지 가능한 상태:
        - Pending (대기 중): 작업이 아직 완료되지 않았고, 아직 성공하거나 실패하지 않은 상태
        - Fulfilled (이행됨): 작업이 성공적으로 완료되었음을 나타내는 상태
        - Rejected (거부됨): 작업이 실패했음을 나타내는 상태
   2. **PromiseResult (프로미스 결과)**:
      - **`PromiseResult`**는 **`Promise`** 객체가 이행되거나 거부된 후의 결과를 나타냄
      - **`Fulfilled`** 상태에서는 작업의 결과가 포함
      - **`Rejected`** 상태에서는 실패의 원인을 나타내는 오류 객체가 포함
   3. **PromiseFullfillReactions (프로미스 이행 처리)**:

      - **`PromiseFullfillReactions`**은 **`Promise`**가 이행되었을 때 실행되는 콜백 함수들의 모음
      - 이 콜백 함수들은 **`then()`** 메서드를 사용하여 **`Promise`** 객체에 추가된다.
      - **`then()`** 메서드의 첫 번째 매개변수로 제공되는 함수가 **`Fulfilled`** 상태일 때 실행된다.

      - 프로미스가 이행되면 이 핸들러는 [[PromiseResult]]의 값을 인자로 전달받고, Microtask Queue로 핸들러를 집어 넣게 된다.

   4. **PromiseRejectReactions (프로미스 거부 처리)**:
      - **`PromiseRejectReactions`**은 **`Promise`**가 거부되었을 때 실행되는 콜백 함수들의 모음
      - **`catch()`** 메서드나 **`then()`** 메서드의 두 번째 매개변수로 제공되는 함수가 **`Rejected`** 상태일 때 실행됨
   5. **PromiseIsHandled (프로미스 처리 여부)**:
      - **`PromiseIsHandled`**는 **`Promise`** 객체가 처리되었는지 여부를 나타내는 플래그
      - **`Promise`** 객체가 처리되었는지 여부를 추적할 수 있으며, 처리되지 않은 **`Promise`** 객체는 경고나 오류를 발생시킬 수 있다.

1. Promise Capability 레코드 생성
   이는 프로미스를 "캡슐화"하고 프로미스를 이행(resolve)하거나 거부(reject)하기 위한 부가 기능을 더한다.
   이들은 최종적으로 프로미스의 [[PromiseState]]와 [[PromiseResult]]를 제어하고 비동기 작업을 시작하는 함수이다.

# 기명 함수와 익명 함수

기명 함수(named function)는 이름이 있는 함수라는 뜻이다.
이 함수는 정의될 때 이름을 지정하며, 이 이름을 사용하여 함수에 접근하거나 호출할 수 있다.

```typescript
function add(x, y) {
  // add 라는 이름의 기명 함수
  return x + y;
}

console.log(add(2, 3)); // 결과: 5
```

### 함수 표현식

함수 표현식은 기명 함수와 익명 함수 둘 다 가능하다.

```typescript
const multiply = function multiply(x, y) {
  return x * y;
};

console.log(multiply(2, 3));

const ayden = function () {
  console.log("익명 함수 실행");
};
```

### 참고로 함수표현식은 익명함수로 쓰든 기명함수로 쓰든 호이스팅이 되지 않는다는 사실은 변함이 없다.
