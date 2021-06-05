# 강제변환(1) - 명시적 변환과 암시적 변환
***
### **명시적 변환과 암시적 변환의 경계*
*적어도 이 책에서는, 명시적이냐 암시적이냐의 근거는 '관용성'에 달려있다 - 관용적으로 널리 쓰이고 있다면 '명시적'이다.*


### 1.추상연산 - 강제변환의 기본 규칙
#### 1)toString
일반적인 원시값은 그대로 문자열화된다.
일반객체는 `toString()`메서드의 상태에 따라 결과값이 달라지는데, 따로 지정하지 않는다면 내부 `[[class]]`를 반환한다.
배열은 지정된 `toString()`메서드가 있어 모든 원소값을 콤마로 분리한 문자열을 반환한다.
#### 2)toNumber
객체와 배열에서의toNumber의 작동방식
>1.toPrimitive추상 연산 과정에서 해당 객체가 `valueOf()` 메서드를 구현했는지 확인 
>2.`valueOf()`를 쓸 수 있고 && 그 값이 원시 값이면 그대로 변환
>3.2번이 false인 경우 (`toString()`메서드가 존재한다면) `toString()`을 이용하여 변환

이후 숫자로 변환

*cf) `Object.create(null)`로 객체 생성시 위의 강제변환이 불가능하게끔 객체를 생성함*
####3)toBoolean
*__Falsy values__*
*undefined, null, false, 0(+0,-0), NaN, ""(빈 문자열)*
위 값을 제외한 모든 값은 truthy

*cf) falsy Object :  브라우저 객체 중 `document.all`(deprecated)는 레거시코드 때문에 object임에도 false 반환*

### 3.명시적 강제변환

#### 3.1숫자의 명시적 강제변환
##### 3.1-1)문자열 <-> 숫자
1.`String()`,`Number()`사용
2.`+`(단항연산자로 숫자로의 강제변환 연산자) 사용 / `-`(숫자로의 강제변환 + 부호변환)
##### 3.1-2)날짜 -> 숫자
1.`+` 사용 (`+new Date()`)
2.`new Date().getTime()` / `Date.now()`(현재 타임스탬프)
##### 3.1-3) `~`(tilde)
정의에 따르면 `~`는 비트단위의 `NOT` 연산자이다. 숫자x에 `~`를 취하면 대략 `-(x+1)`과 같은 값이 나온다. 
이 성질을 이용하여 -1을 리턴할 수 있는 기본 메서드들을 이용한 코드를 줄일 수 있댜 (`~(-1)`은 (-)0이다)
```js
if(str.indexOf("x") === -1) { /*codes*/ }

if(!~str.indexOf("x")) { /*codes*/ }
```
#### 3.2 명시적 강제변환:파싱
`parseInt()`를 사용
주의: 기본적으로 문자열을 인수로 사용해야함. 두번째 인수로 들어오는 숫자는 진법기준을 의미한다.
`parseInt()`는 문자열 속 숫자를 인식하여 숫자로 파싱하다가 문자열 속 숫자가 아닌 문자를 만나면 파싱을 멈춘다

#### 3.3 Boolean으로의 강제변환
1.`Boolean()`사용
2.`!!`사용

### 4.암시적 (강제)변환

#### 4.1숫자의 암시적 강제변환
##### 4.1-1)문자열 <-> 숫자
`숫자 + ""`형식으로 변환 (교환볍칙 성립)
cf) + 연산자의 양쪽이 문자열이 아닌 경우 : 객체의 `toNumber()` 작동방식과 유사하지만 마지막에 숫자로 변환이 아닌 문자열 반환
그러므로 객체 의 `toValue()`, `toString()` 메서드의 상태에 따라 명시적 변환과 암시적 변환의 결과값이 달라질 수 있다
(객체의 명시적 문자열변환은 단순한 메서드호출이므로)

##### 4.1-2)불린 -> 숫자
여러 인자 중 하나만 true/truthy 한지를 검출하는 로직 구현의 경우 falsy한 경우(NaN)를 filter해준다면 명시적인 경우보다 훨씬 간결하게 구현가능
```js
//명시
function onlyOne() {
  let sum = 0;
  for(let i = 0; i<arguments.length; i++) {
    sum += Number( !!arguements[i]);
  }
  return sum === 1;
}
//암시
function onlyOne() {
  let sum = 0;
  for(let i = 0; i<arguments.length; i++) {
    //needs filter for falsy values like NaN but 0 (0 needs to count)
    sum += arguements[i];
  }
  return sum === 1;
}
```