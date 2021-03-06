# 렉시컬 스코프

스코프는 두 가지 방식으로 작동한다. 첫 번째는 렉시컬 스코프, 두 번째는 동적 스코프 이다. 자바스크립트에선 렉시컬 스코프를 차용한다.

## 렉스타임

렉싱 처리 과정에서 소스 코드 문자열을 분석해 상태 유지 파싱의 결과로 생성된 토큰에 의미를 부여한다. 이 개념이 렉시컬 스코프가 무엇인지, 어원이 어디인지를 알게 해주는 바탕이 된다.

순환적인 정의를 하자면 렉시컬 스코프는 렉싱 타임에 정의되는 정적 스코프다. 바꿔 말하면 렉시컬 스코프는 개발자가 코드를 짤 때 변수와 스코프 블록을 어디서 작성하는가에 기초해 렉서가 코드를 처리할 때 확정된다.

스코프 블록은 서로 중첩될 수 있다. 그러나 어떤 함수의 버블도 동시에 다른 두 스코프 버블 안에 존재할 수 없다. 어떤 함수도 두 개의 부모 함수 안에 존재할 수 없는 것 처럼 말이다.

### 검색

엔진은 스코프 버블의 구조와 상대적 위치를 통해 어디를 검색해야 확인자를 찾을 수 있는지 안다.
검색은 가장 안쪽의 스코프 버블에서 시작한다. 여기서 변수를 찾지 못하면 다음으로 가장 가까운 스코프 버블로 한 단계 올라가고, 변수가 있으면 찾아 사용한다.
스코프는 목표와 일치하는 대상을 찾는 즉시 검색을 중단한다. 이를 섀도잉 이라고 한다. 섀도잉과 상관없이 스코프 검색은 항상 실행 시점에서 가장 안쪽 스코프에서 시작해 최초 목표와 일치하는 대상을 찾으면 멈추고, 그전까지는 바깥/위로 올라가면서 실행한다.

글로벌 변수는 자동으로 웹 브라우저의 window 같은 글로벌 객체에 속한다. 따라서 글로벌 변수를 직접 렉시컬 이름으로 참조하는 것뿐만 아니라 글로벌 객체의 속성을 참조해 간접적으로 참조할 수도 있다.
글로벌 변수에는 `window.변수명`을 통해 접근할 수 있다. 가려져 있어 다른 방식으로 접근할 수 없는 글로벌 변수에는 이 방법을 통해 접근할 수 있다. 그래서 글로벌이 아닌 섀도 변수는 접근할 수 없다.

어떤 함수가 어디서 또는 어떻게 호출되는지에 상관없이 함수의 렉시컬 스코프는 함수가 선언된 위치에 따라 정의된다.

## 렉시컬 속이기

렉시컬 스코프는 개발자가 작성할 때 함수를 어디에 선언했는지에 따라 결정된다. 이런 렉시컬 스코프를 속일 수 있는 두가지 방법이 있는데, 두 방법 다 권장하지 않는 방법이다. 이는 성능을 떨어뜨리기 때문이다.

### eval()

`eval()`함수는 문자열을 인자로 받아들여 실행 시점에 문자열의 내용을 코드의 일부분처럼 처리한다. 마치 처음 작성될 떄부터 있던 것처럼 실행한다.
`eval()`이 실행된 후 코드를 처리할 때 엔진은 지난 코드가 동적으로 해석되어 렉시컬 스코프를 변경시켰는지 알 수도 없고 관심도 없다. 엔진은 그저 평소처럼 렉시컬 스코프를 검색할 뿐이다.

### with

with는 일반적으로 한 객체의 여러 속성을 참조할 때 객체 참조를 매번 반복하지 않기 위해 사용할 수 있다. 그러나 이 방법의 가장 큰 문제점은 선언되지 않은 속성을 참조하려 할 때 참조에러를 발생하지 않고 암묵적으로 전역변수를 발생시킨다. 이는 with 문이 속성을 가진 객체를 받아 마치 하나의 스코프처럼 취급한다. 따라서 객체의 속성은 모두 해당 스코프 안에 정의된 확인자로 간주된다.
with문 블록안에서 일반적인 var 선언문이 수행될 경우 선언된 변수는 with 블록이 아니라 with를 포함하는 함수의 스코프에 속한다.

eval()은 인자로 받은 코드 문자열에 하나 이상의 선언문이 있을 경우 이미 존재하는 렉시컬 스코프를 수정할 수 있지만,
with 문은 넘겨진 객체를 가지고 난데없이 사실상 하나의 새로운 렉시컬 스코프를 생성한다.

### 성능

eval()과 with()는 어떤 렉시컬 스코프가 수정될지 정확하게 알 수 없고, with()는 렉시컬 스코프가 새로 생성할 수 없기에 엔진에서의 최적화 단계가 의미없어 코드가 느리게 동작된다.

## 정리

렉시컬 스코프란 개발자가 코드를 작성할 때 함수를 어디에 선언하는지에 따라 정의되는 스코프를 말한다.
컴파일레이션의 렉싱 단계에서는 모든 확인자가 어디서 어떻게 선언됐는지 파악해 실행 단계에서 어떻게 확인자를 검색할지 예상할 수 있도록 도와준다.

스코프를 속이는 방법은 코드를 느리게 동작시키므로 사용하지 말도록 하자.