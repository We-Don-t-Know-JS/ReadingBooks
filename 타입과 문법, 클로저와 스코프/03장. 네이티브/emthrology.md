# 네이티브
***
JS의 네티티브란 ECMAScript 명세의 내장객체를 말한다

## 내부 `[[Class]]`
콘솔에서 한번씩 볼 수 있는 `[object Object]`와 같은 문구의 두번째 부분을 가리킴
문자열,숫자,불린 등의 원시값은 박싱과정을 거쳐 `String`,`Number`,`Boolean` 와 같이 나타낸다
`null`,`undefined`등은 생성자는 없지만 내부클래스로 `Null`,`Undefined`를 나타낸다

주의 : `boolean`을 박싱하면 그 자체로는 항상 truthy하기 때문에 의도대로 사용할 수 없다 (`Boolean`은 `object`이므로 항상 truthy)

##생성자
_주의!!: `Array` 생성자의 인자가 숫자 하나인 경우 '그 숫자를 배열의 크기'로 하는 배열이 생성된다._ 
이 경우 슬롯에 값은 없지만 `length`는 값이 존재하는 특이한 배열이 된다.
여기에 빈 슬롯의 표현에 `undefined`가 쓰인다는 점이 이 배열의 해괴함을 더한다.
`undefined`의 중의성은 이전 챕터에서 설명되어있음

__생성자를 사용하는 네이티브 : Date(),Error()__

Symbol() : 특별한 '유일 값'
Symbol의 사용 사례
>https://perfectacle.github.io/2017/04/16/ES6-Symbol/

## 네이티브 프로토타입
네이티브 프로토타입은 '빈 상태'(e.g. Array.prototype -> 빈 배열)
그러므로 프로토타입으로 디폴트값을 세팅하는것도 좋은 아이디어
주의: 네이티브 프로토타입을 임의로 변경해서 사용하지말자
