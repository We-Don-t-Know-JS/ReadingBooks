# 타입
타입이 없는 것이 아니다. _변수에 할당 된 데이터_ 에 타입이 있음

## JS의 7가지 내장 타입 
js의 타입검사 키워드 :`typeof`

주의 : `null`을 `typeof`로 검사해보면 'object'가 리턴됨 
```javascript
typeof null //"object"
```
주의 : 함수를 `typeof`로 검사해보면 'function'이 리턴됨 (실제로 함수는 object의 하위타입)
```javascript
typeof function(){} //"function" 
```
배열도 `object`의 하위타입이다. 
  실무에서 `object`와 `array`를 구분하는법 
  1.instanceof

  2.Array.isArray() 

  3.library (underscore, lodash, etc.)

cf)case of 'not a number'
```javascript
typeof NaN //"number"
```
## undefined 와 null
`undefined` : 선언되고 데이터가 할당되지 않은 변수의 default value
`null`: '값이 없음'을 명시적으로 표현함

 참조에러를 방지하기위한 코드는 undefined check를 해야함 (null check가 아니라)