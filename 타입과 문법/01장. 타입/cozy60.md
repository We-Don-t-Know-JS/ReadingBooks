# 타입
자바스크립트 같은 동적 언어는 타입 개념이 없다고 생각하기 쉽다. 

> ECMAScript 개발자가 ECMAScript 언어를 이용해 직접 조작하는 값들의 타입이 바로 ECMAScript 언어 타입이다. ECMAScript 언어 타입에는 Undefined, Null, Boolean, String, Number, Object가 있다.

`타입`에 대해 간단히 정의내리자면 자바스크립트 엔진, 개발자 모두에게 어떤 값을 다른 값과 분별할 수 있는 고유한 내부 특성의 집합이다.

다시 말하자면, 기계(엔진)와 사람(개발자)이 42(숫자)란 값을 "42"(문자열)값과 다르게 취급한다면, 두 값은 타입이 서로 다르다.

## 타입의 실체
자바스크립트 프로그램에서 강제변환은 다양한 방식으로 일어난다. 

### 내장 타입
자바스크립트의 7가지 내장 타입. 이들은 또다른 말로 원시타입, Primitives type 이라고도 얘기한다.
* null
* undefined
* boolean
* number
* object
* symbol

값 타입은 `typeof` 연산자로 일 수 있다. 그러나 `typeof`의 반환 값은 항상 7가지 내장 타입값과 정확히 일치하지 않는다. 
```js
typeof null === "object"; // true
```

타입으로 null 값을 정확히 확인하려면 조건이 더 필요하다.
```js
var a = null;
// a가 null이거나 undefined와 같은 값일 때 
// typeof a==="object" 를 사용하면 
(!a && typeof a === "object"); // true
```

null은 falsy한(false나 다름없는) 유일한 원시 값이지만, 타입은 `object`인 특별한 존재다.

typeof가 반환하는 문자열은 한가지 더 존재한다.
```js
typeof function a(){ /* ... */ } === "function"; // true
```
반환 값을 보면 function이 최상위 레벨의 내장 타입처럼 보이지만 명세를 보면 실제로는 `object`의 **하위 타입**이다. (아마 독립적인 `function`이라는 내장 타입이 아닌 객체의 하위 타입이라는걸 얘기하고 싶은듯 하다.) 
구체적으로 설명하면, **함수는 호출 가능한 객체**라고 명시되어 있다.

배열 또한 추가 특성을 지닌, 객체의 **하위 타입**이다.
```js
typeof [1,2,3] === "object"; // true
```

## 값은 타입을 가진다
값에는 타입이 있지만, 변수는 따로 타입이 없다. 변수는 언제라도, 어떤 형태의 값이라도 가질 수 있다. 

자바스크립트는 타입 강제를 하지 않는다. 변숫값이 처음 할당된 값과 동일한 타입일 필요는 없다. 문자열을 넣었다 후에 숫자를 넣어도 상관없다.

변수에다가 `typeof`연산자를 사용할 때, "이 변수의 타입은 무엇인가?" 라는 질문과 같지만, 자세히 설명하자면 변수에는 타입이 없으므로 "변수 안에 할당된 값의 타입은 무엇인가?" 라고 묻는 것과 같다.

### 값이 없는 vs 선언되지 않은
값이 없는 변수의 값은 undefined이며, typeof 결과는 "undefined"이다. 
```js
var a;
typeof a;
"undefined"

var b = 42;
var c;
b=c;

typeof b;
"undefined"
typeof c;
"undefined"
```
값이 없는 undefined와 선언되지 않은 undeclared(not defined)를 동의어처럼 생각하기 쉬운데, 자바스크립트에서 둘은 완전히 다른 개념이다.

undefined는 접근 가능한 스코프에 변수가 선언되었으나 현재 아무런 값도 할당되지 않은 상태를 뜻한다.
undeclared는 접근 가능한 스코프에 변수 자체가 선언조차 되지 않았음을 의미한다. 
```js
var a;

a; // undefined
b; // ReferenceError: b is not defined, undeclared
```
선언되지 않은 변수 또한 `typeof`를 사용하면 undefined로 값이 반환된다. 이는 `typeof`만의 독특한 안전가드 이다. 

### 선언되지 않은 변수 
여러 스크립트 파일의 변수들이 전역 네임스페이스를 공유할 때, `typeof`의 안전가드는 의외로 쓸모가 있다. 

만약 `var DEBUG=true`라는 변수를 최상위 스코프에 선언했을 때, 나머지 어플리케이션에서 참조 에러가 발생하지 않도록 `DEBUG`전역 변수를 체크헤야 한다.
```js
// error
if (DEBUG)  {
  console.log("디버깅을 시작합니다.");
}

// 안전하게 존재 여부를 확인하는 방법
if (typeof DBUG !== "undefined") {
  atob = function() { /* ... */ };
}
```

## 정리
* 자바스크립트에는 7가지 내장 타입(null, undefined, boolean, number, string, object, symbol)이 존재한다. `typeof` 연산자로 타입명을 알아낸다.
* 변수는 타입이 없지만 변수 안의 값은 타입이 있다. 타입은 내재된 특성을 정의한다.
* undefined와 undeclared는 같지 않다. undefined는 선언된 변수에 할당할 수 있는 값이지만, undeclared는 변수 자체가 선언된 적이 없는것을 의미한다.