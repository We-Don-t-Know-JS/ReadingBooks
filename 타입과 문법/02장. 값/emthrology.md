# 값 Value
***
## 배열 

배열 크기`length`는 단순 프로퍼티, 고정이 아님
인덱스에 `number`가 아닌 다른 타입 데이터가 들어올 수 있지만, 권장사항이 아님 

주의 : `delete`연산자로 배열의 모든 원소를 제거하더라도 해당 배열의`length` 는 변하지 않음 
```js
//배열 delete
let arr = [1,2,3,4,5];
for(let i; i<arr.length; i++) {
  delete arr[i];
}

console.log(arr.length); //5
```

##유사배열

유사배열은 숫자인덱스가 가리키는 값들의 집합을 말하며, 배열의 protptype methods를 사용할 수 없다.
유사배열에서 배열의 methods를 사용하는 방법 : `call()` 과 배열 유틸리티 함수 `indexof()`,`concat()`,`forEach()`,`slice()`등
```js
//유사배열
function toArray() {
  return Array.prototype.slice.call(arguments);
}

console.log(typeof toArray('foo','bar')); //true

//es6 - Array.form()
function foo() {
  return Array.from(arguments);
}

console.log(foo("bar","baz")); //["bar","baz"]
```

##문자열

유사배열이다. 
불변성이다. (내용변경하지 않고 새로운 문자열 생성)
인덱스로 접근 지양 (`charAt([index])`로 접근)

불변배열메서드는 문자열에 적용 가능하지만, 유니코드가 섞여있는 경우에는 사용불가능

```js
//문자열
const str = "bar";
console.log(str[1]); //makes sence, but not universal (e.g. IE)
console.log(str[12]); //return undefined
console.log(str.charAt(12)) //return ""
```


## 숫자

JS는 정수형 숫자가 없고 모두 부동소숫점 숫자로 표현된다

숫자 리터럴에 곧바로 메서드 불러올 수 있지만, 쓰지말자

숫자의 지수형 표기법 : E
```js
1E3 //1*10^3
```
부동소숫점 관련 이슈 해결책 : `Number.EPSILON`

`isNaN()` 대신 `Number.isNaN()` 사용 : 
전자는`number`가 아니기만 하면 `true` 이지만 후자는 주어진 값의 유형이 `Number`이고 값이 `NaN`이면 `true`, 아니면 `false`

js에는 `+0` `-0`이 따로 존재한다. 일상상황에서의 혼동을 '예방'하기 위해 두 값은 거의 모든 상황에서 동일한 값으로 취급된다
```js
//differentiate +0 from -0
function isNegZero(n) {
  n = Number(n);
  return n === 0 && 1/n === -Infinity;
}
```


##값 vs 레퍼런스
JS의 레퍼런스는 오직 '자신의 값'만을 가리킨다.
```javascript
let a = [1,2,3];
let b = a;
b = [4,5,6];
console.log({a,b}) //[1,2,3],[4,5,6]


function foo(x) {
  x.push(4);
  x = [4,5,6];
  x.push(7);
}
let a = [1,2,3];
foo(a);
console.log(a) //[1,2,3,4]
```
함수의 경우 특히 레퍼런스 때문에 의도한 바와 다른 결과가 초래될 수 있으므로 주의를 요한다
