# var, let, const

인터넷을 보다 보면 아무런 설명 없이 var을 쓰는것은 좋지 않다고 한다.  
함수 스코프, 재선언, 구식 문법이라고만 알고있는 var.  
왜 쓰지 않아야 하는지 자세히 살펴 보자.

```javascript
var name = "이름";

var name = "김성태";
```

아무런 에러가 발생하지 않는다.  
var은 재선언이 가능하기 때문.

```javascript
console.log(name); // undefined
var name = "김성태";
```

선언 이전에 호출시 undefined를 출력.

반면 let, const는 재 선언이 불가능하다.

```javascript
let name = "이름";

let name = "김성태"; // SyntaxError
```

```javascript
console.log(name); // RefrenceError, TDZ
let name = "김성태";
```

```javascript
const name = "이름";

name = "김성태"; // Syntax Error
```

var, let, const는 위와같은 특징을 갖고 있다.

자세한 내용은 Study Repository의 JavaScript에서 다루었으니 특징만 알고 넘어가도록 하자.

## 함수 스코프와 블록 스코프

```javascript
var global = "전역";

if (global === "전역") {
  var global = "지역";

  console.log(global); // 지역
}

console.log(global); // 지역
```

블록 내부에서 변수를 선언했으나 전역 변수까지 바뀌었다.

var는 함수 스코프를 따르기 때문.

```javascript
let global = "전역";

if (global === "전역") {
  let global = "지역";

  console.log(global); // 지역
}

console.log(global); // 전역
```

let, const의 경우 블록스코프를 따르기 때문에 원하는 결과를 얻었다.

참고로 블록 스코프는 함수 스코프를 포함하는 관계에 있다.

```javascript
// 함수 스코프이기도 하고 블록 스코프이기도 함.
function getData() {
  // do something
}

// 함수 스코프는 아니지만 블록 스코프
{
  // do something
}
```

var사용을 지양하고, 웬만하면 const를 사용하도록 하자.  
why ? const의 경우 재할당이 불가능하다고 했다.  
객체나 배열같은 레퍼런스 객체 조작에는 문제가 없다.  
그러므로 재할당이 불가능한 const를 사용하자.

## 전역 공간

전역 공간은 최상위를 의미하고, 브라우저에선 window, NodeJS에서는 global 을 의미한다.

- index.js

```javascript
var globalVar = "global";

console.log(globalVar);
```

- index2.js

```javascript
console.log(globalVar);
// 결과는 ??
```

접근이 가능하다.

뿐 만 아니라 global에서 변수가 등록되어 있는것을 볼 수 있다.

```javascript
console.log(window.globalVar);
// 결과는 동일하다.
```

그래서, 파일을 나눈다고 해도, Scope가 나뉘지 않는다.

```javascript
for (var index = 0; index < array.length; index++) {
  console.log(array[index]);
}

// 이렇게 실행하면 window.index 에 3 등록된다.
```

결론 : 전역 공간 사용을 최소화하자.

이유 : 어디서나 접근이 가능하기 때문.

그래서... ?

전역 변수를 만들지 않고 웬만하면 지역 변수를 사용하자.  
window와 global 조작을 삼가하자.  
const, let을 사용하자.  
IIFE와 모듈, Closure를 사용하자.

## 임시 변수

전역 공간 사용을 최소화하자 했다.

임시 변수는 무엇? -> scope 내부에서 전역 변수처럼 사용되는 변수

scope 내부에서 쓰는데 왜 ?

일단 함수가 커지면 마치 전역공간처럼 될 수 있다.

그리고 유혹에 빠질 수 있는데, 임시변수를 조작해 통해 로직을 구성하는 경우, 기능을 추가해야 할 때 어떻게 할것인가? -> 기존 로직에 덧붙이기 또는 함수 쪼개기.

임시변수를 사용하는 경우 기존 로직에 덧붙이는 것이 일단은 가장 간단한 방법이다. -> 유혹에 빠지기 쉬움

이러한 덧붙이기가 계속되는경우 결국 함수가 커지게 되고, 전역공간처럼 될 수 있다는 것.

```javascript
function getRandomNumber(min, max) {
  const result = {};
  result.value = Math.floor(Math.random() * (max - min) + min);

  result.range = [min, max];

  return result;
}
```

추가적인 수정이 필요한 경우 함수 내부에서 result를 조작하는 로직이 제일 쉬울 수 있다. -> 유혹에 빠질 수 있음. (단일 책임원칙 위배)

명령형으로 가득한 로직을 탈피하자.

값 예측이 힘들고, 디버깅이 어렵게 된다.

```javascript
function getRandomNumber(min, max) {
  return {value : Math.floor(Math.random() * (max - min) + min) range: [min, max]};
}
```

이런식으로 만들고, 로직 추가시 함수를 하나 더 추가해 내부에서 호출해 사용하자.

해결책이 더 있는데,

1. 함수 나누기
2. 바로 반환
3. 고차함수 (map, filter, reduce)
4. 선언현 프로그래밍으로 만들기

## 호이스팅

선언과 할당이 분리되어, 선언이 최상단으로 끌어올려 짐.

```javascript
var global = 10;

function call() {
  console.log(global);
  var global = 5;
}
```

결과는 ? undefined.

why ? global의 선언이 호이스팅 됨.

그래서, 5를 출력하고 싶었지만 undefined를 출력할 수 있다.

런타임에 와서야 결과를 볼 수 있음.

let, const는 TDZ를 통해 위와같은 실행을 미리 방지가 가능.

함수도 호이스팅 되기 때문에, 함수 선언식보다는 let, const를 통해 표현식 사용을 권장한다.

호이스팅에 대한 자세한 내용은, Study의 Hoisting에서 자세하게 다루기 때문에 간단하게 하고 넘어가도록 하자.
