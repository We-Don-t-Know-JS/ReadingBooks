
# You Don't Know JS: Types & Grammar
# Chapter 2: Values

크게 배열, 스트링, 숫자가 기본인데 자바스크립트는 다른 특징들이 있다.

## 배열

다른 타입 지정 언어와 달리 아무런 값을 저장할 수 있는 컨테이너이다. 사전에 배열 크기를 지정 할 필요도 없다.

```js
var a = [ 1, "2", [3] ];

a.length;		// 3
a[0] === 1;		// true
a[2][0] === 3;	// true
```

**경고:** `delete`하면 배열에서 아이템이 지워지지만 그냥 비어만 있는 상태이기 때문에 length했을때의 크기는 동일하다. 다른 파이썬에서는 중간을 지우면 한칸씩 앞당겨지는.

```js
var a = [ ];

a[0] = 1;
// no `a[1]` slot set here
a[2] = [ 3 ];

a[1];		// undefined

a.length;	// 3
```

```py
a = [1,2,3]

del a[1]

print(a) // [1.3]
print(len(a)) // 2
```

배열에 인덱스 대신 문자로도 값은 지정 가능하나 좋은 방식은 아니다. object과 동일하게 되며 헷갈리기 때문.

```js
var a = [ ];

a[0] = 1;
a["foobar"] = 2;

a.length;		// 1
a["foobar"];	// 2
a.foobar;		// 2
```

그리고 배열의 크기를 할 때도 원하지 않는 값이 나온다. 형 변환이 일어나기 때문

```js
var a = [ ];

a["13"] = 42;

a.length; // 14
```

### Array-Likes

배열 같은 것을 진짜 배열로 변환하기 위해서는 slice 유틸리티를 이용하면 된다.

```js
function foo() {
	var arr = Array.prototype.slice.call( arguments );
	arr.push( "bam" );
	console.log( arr );
}

foo( "bar", "baz" ); // ["bar","baz","bam"]
```

ES6부터는 아래의 방법도 가능

```js
...
var arr = Array.from( arguments );
...
```

## Strings

string과 array는 매우 유사하다.

```js
var a = "foo";
var b = ["f","o","o"];
```

```js
a.length;							// 3
b.length;							// 3

a.indexOf( "o" );					// 1
b.indexOf( "o" );					// 1

var c = a.concat( "bar" );			// "foobar"
var d = b.concat( ["b","a","r"] );	// ["f","o","o","b","a","r"]

a === c;							// false
b === d;							// false

a;									// "foo"
b;									// ["f","o","o"]
```

그러면 100% 동일한가? **No**:

```js
a[1] = "O";
b[1] = "O";

a; // "foo"
b; // ["f","O","o"]
```

자바스크립트에서 string은 immutable이고 array는 quite mutable이다. 그래서 위에서 스트링은 값이 바뀌지 않고 배열은 바뀐다.

```js
c = a.toUpperCase();
a === c;	// false
a;			// "foo"
c;			// "FOO"

b.push( "!" );
b;			// ["f","O","o","!"]
```

배열은 .reverse()를 통해 바로 역순 가능하지만 스트링은 불가능하기 때문에 배열로 변환하고 역순 후 다시 스트링으로 변환해야 한다.

```js
var c = a
	// split `a` into an array of characters
	.split( "" )
	// reverse the array of characters
	.reverse()
	// join the array of characters back to a string
	.join( "" );

c; // "oof"
```


## Numbers

자바스크립트는 숫자에 관해 오직 `number`만 가지고 있다. 다른 언어에서는 int, float, double인 반면.

자바스크립트에서 숫자는 "IEEE 754" 표준, "floating-point"로 정의 된다. "double precision" format (aka "64-bit binary").

### Numeric Syntax

```js
var a = 42;
var b = 42.3;
```

앞에 0은 옵셔널


```js
var a = 0.42;
var b = .42;
```

뒤에 0도 옵셔널

```js
var a = 42.0;
var b = 42.;
```

출력할 때는 기본으로 뒤에 0을 다 제거해서 보여준다.

```js
var a = 42.300;
var b = 42.0;

a; // 42.3
b; // 42
```

매우 크거나 작은 숫자는 exponential 포멧으로 보여준다.

```js
var a = 5E10;
a;					// 50000000000
a.toExponential();	// "5e+10"

var b = a * a;
b;					// 2.5e+21

var c = 1 / a;
c;					// 2e-11
```

`Number.prototype`을 사용해서 여러가지 메서드 사용 가능하다. 숫자는 Number()로 매핑되어 있기 때문.

```js
var a = 42.59;

a.toFixed( 0 ); // "43"
a.toFixed( 1 ); // "42.6"
a.toFixed( 2 ); // "42.59"
a.toFixed( 3 ); // "42.590"
a.toFixed( 4 ); // "42.5900"
```

`toPrecision(..)`는 유효숫자

```js
var a = 42.59;

a.toPrecision( 1 ); // "4e+1"
a.toPrecision( 2 ); // "43"
a.toPrecision( 3 ); // "42.6"
a.toPrecision( 4 ); // "42.59"
a.toPrecision( 5 ); // "42.590"
a.toPrecision( 6 ); // "42.5900"
```

아래와 같이 .이 숫자 .인지 메서드 .인지 헷갈리니 `숫자는 괄호로` 감싸는게 좋아보임.

```js
// invalid syntax:
42.toFixed( 3 );	// SyntaxError

// these are all valid:
(42).toFixed( 3 );	// "42.000"
0.42.toFixed( 3 );	// "0.420"
42..toFixed( 3 );	// "42.000"
```

`number`는 exponential form으로도 지정 가능하다.

```js
var onethousand = 1E3;						// means 1 * 10^3
var onemilliononehundredthousand = 1.1E6;	// means 1.1 * 10^6
```

`number` 2진, 8진, 16진으로도 지정 가능하다.

```js
0xf3; // hexadecimal for: 243
0Xf3; // ditto

0363; // octal for: 243
```

헷갈리기 때문에 0 다음에는 소문자를 사용해서 지정 할 것.

### Small Decimal Values

부동소숫점으로의 단점은 아래 결과와 같다. 

```js
0.1 + 0.2 === 0.3; // false
```

정확하게 0이 아니기 때문에 false이고 이를 해결하기 위해서는 매우 작은 값인 eplison이 필요하다.

```js
if (!Number.EPSILON) {
	Number.EPSILON = Math.pow(2,-52);
}
```

`Number.EPSILON` 이용해서 정확하게 비교가 가능하다.

```js
function numbersCloseEnoughToEqual(n1,n2) {
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}

var a = 0.1 + 0.2;
var b = 0.3;

numbersCloseEnoughToEqual( a, b );					// true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );	// false
```

### Safe Integer Ranges

값이 너무 크면 정확하게 표현이 안될 수가 있다. 그래서 표현 가능한 최대값인 `Number.MAX_SAFE_INTEGER`가 . Unsurprisingly, there's a minimum value, `-9007199254740991`, and it's defined in ES6 as `Number.MIN_SAFE_INTEGER` 가 있다.

실제로 이런 값을 접할 케이스는 DB에서 64bit 아이디를 가져와서 사용하는 경우. 혹시나 이런 경우에는 string으로 지정하면 안전하게 가능하다.

### Testing for Integers

정수인지 체크하는 방법 `Number.isInteger(..)`:

```js
Number.isInteger( 42 );		// true
Number.isInteger( 42.000 );	// true
Number.isInteger( 42.3 );	// false
```

To polyfill `Number.isInteger(..)` for pre-ES6:

```js
if (!Number.isInteger) {
	Number.isInteger = function(num) {
		return typeof num == "number" && num % 1 == 0;
	};
}
```

이전에 safe와 이어서 체크하는 방법 `Number.isSafeInteger(..)`:

```js
Number.isSafeInteger( Number.MAX_SAFE_INTEGER );	// true
Number.isSafeInteger( Math.pow( 2, 53 ) );			// false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 );		// true
```

To polyfill `Number.isSafeInteger(..)` in pre-ES6 browsers:

```js
if (!Number.isSafeInteger) {
	Number.isSafeInteger = function(num) {
		return Number.isInteger( num ) &&
			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
	};
}
```

## Special Values

몇가지 *주의* 해야할 스페셜 값들이 있다.

### The Non-value Values

For the `undefined` type, there is one and only one value: `undefined`. For the `null` type, there is one and only one value: `null`. So for both of them, the label is both its type and its value.

Both `undefined` and `null` are often taken to be interchangeable as either "empty" values or "non" values. Other developers prefer to distinguish between them with nuance. For example:

* `null` is an empty value
* `undefined` is a missing value

Or:

* `undefined` hasn't had a value yet
* `null` had a value and doesn't anymore

Regardless of how you choose to "define" and use these two values, `null` is a special keyword, not an identifier, and thus you cannot treat it as a variable to assign to (why would you!?). However, `undefined` *is* (unfortunately) an identifier. Uh oh.

### Undefined

In non-`strict` mode, it's actually possible (though incredibly ill-advised!) to assign a value to the globally provided `undefined` identifier:

```js
function foo() {
	undefined = 2; // really bad idea!
}

foo();
```

```js
function foo() {
	"use strict";
	undefined = 2; // TypeError!
}

foo();
```

In both non-`strict` mode and `strict` mode, however, you can create a local variable of the name `undefined`. But again, this is a terrible idea!

```js
function foo() {
	"use strict";
	var undefined = 2;
	console.log( undefined ); // 2
}

foo();
```

#### `void` Operator

실제로 undefined는 값이 지정되지 않은 것이지만 void를 통해 undefined로 할 수 도 있다. 단, 실제로 값이 undefined로 바뀌지는 않는다.

```js
var a = 42;

console.log( void a, a ); // undefined 42
```

리턴으로 void를 하고 싶은 경우 아래처럼 사용한다. setTimeout 함수가 숫자를 리턴하기 때문.

```js
function doSomething() {
	// note: `APP.ready` is provided by our application
	if (!APP.ready) {
		// try again later
		return void setTimeout( doSomething, 100 );
	}

	var result;

	// do some other stuff
	return result;
}

// were we able to do it right away?
if (doSomething()) {
	// handle next tasks right away
}
```

동일한 로직을 아래와 같이 다른 라인으로 분리해서도 가능. 많은 개발자가 선호하는 방식.

```js
if (!APP.ready) {
	// try again later
	setTimeout( doSomething, 100 );
	return;
}
```

일반적으로는 값이 다 지정되어 있지만 없는 경우에는 void를 이용


### Special Numbers

`number` type은 여러가지 있다.

#### The Not Number, Number

`NaN` 는 "not a `number` 숫자가 아니다. NaN 값으 타입은 `숫자`

For example:

```js
var a = 2 / "foo";		// NaN

typeof a === "number";	// true
```

```js
var a = 2 / "foo";

a == NaN;	// false
a === NaN;	// false
```

`NaN` 는 특별해서 다른 NaN과도 다르다.

```js
var a = 2 / "foo";

isNaN( a ); // true
```

uilt-in global utility `isNaN(..)` 이용해서 NaN인지 확인 가능.

빠르지는 않다.

isNaN의 단점은 숫자가 아니다라는 의미를 정확히 수행하기 때문에 문제가 있다.

```js
var a = 2 / "foo";
var b = "foo";

a; // NaN
b; // "foo"

window.isNaN( a ); // true
window.isNaN( b ); // true -- ouch!
```

ES6 되어서야 드디어 `Number.isNaN(..)` 가 생겼다.

```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return (
			typeof n === "number" &&
			window.isNaN( n )
		);
	};
}

var a = 2 / "foo";
var b = "foo";

Number.isNaN( a ); // true
Number.isNaN( b ); // false -- phew!
```

polyfill은 쉽게 구현 가능하다. 같은 변수가 서로 같지 않으니
```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return n !== n;
	};
}
```

#### Infinities

+와 - 무한이 존재

```js
var a = 1 / 0;	// Infinity
var b = -1 / 0;	// -Infinity
```

숫자가 너무 커져 Infinity가 되면 다시 돌아 갈수가 없다.

#### Zeros

0이 0과 -0이 존재한다.

```js
var a = 0 / -3; // -0
var b = 0 * -3; // -0
```

-0이라도 문자열로 출력하면 "0"으로 출력

```js
var a = 0 / -3;

// (some browser) consoles at least get it right
a;							// -0

// but the spec insists on lying to you!
a.toString();				// "0"
a + "";						// "0"
String( a );				// "0"

// strangely, even JSON gets in on the deception
JSON.stringify( a );		// "0"
```

신기하게 반대로 다시 string에서 숫자로 하면 정상적으로 -0이 출력

```js
+"-0";				// -0
Number( "-0" );		// -0
JSON.parse( "-0" );	// -0
```

==, === 비교해도 0과 -0은 같기 때문에 정확히 구분을 위해서는 아래와 같이 해야한다.

```js
function isNegZero(n) {
	n = Number( n );
	return (n === 0) && (1 / n === -Infinity);
}

isNegZero( -0 );		// true
isNegZero( 0 / -3 );	// true
isNegZero( 0 );			// false
```

몇가지 케이스들에 한해 방향을 가지고 있어야하기 때문에 0도 +/-를 가지고 있다.

### Special Equality

ES6부터 2개의 값일 정확히 비교하는 방법이 생겼다.

```js
var a = 2 / "foo";
var b = -3 * 0;

Object.is( a, NaN );	// true
Object.is( b, -0 );		// true

Object.is( b, 0 );		// false
```

There's a pretty simple polyfill for `Object.is(..)` for pre-ES6 environments:

```js
if (!Object.is) {
	Object.is = function(v1, v2) {
		// test for `-0`
		if (v1 === 0 && v2 === 0) {
			return 1 / v1 === 1 / v2;
		}
		// test for `NaN`
		if (v1 !== v1) {
			return v2 !== v2;
		}
		// everything else
		return v1 === v2;
	};
}
```

`Object.is(..)` 는 ==, ===로 비교가 가능한 케이스에는 사용하면 안된다.

## Value vs. Reference

자바스크립트에서는 pointer라는 개념이 없다.

문자열인 경우 값으로만 copy되어 지정된다. 그래서 b = a하고 값 변경해도 이전 a가 변하지 않음.

```js
var a = 2;
var b = a; // `b` is always a copy of the value in `a`
b++;
a; // 2
b; // 3

var c = [1,2,3];
var d = c; // `d` is a reference to the shared `[1,2,3]` value
d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
```

Simple values (aka scalar primitives) 는 *항상* value-copy이다: `null`, `undefined`, `string`, `number`, `boolean`, and ES6's `symbol`.

Compound values -- `object`s (including `array`s, and all boxed object wrappers -- see Chapter 3) 와 `function`s 는 *항상* create a **copy of the reference** on assignment or passing.


```js
var a = [1,2,3];
var b = a;
a; // [1,2,3]
b; // [1,2,3]

// later
b = [4,5,6];
a; // [1,2,3]
b; // [4,5,6]
```

하지만 assign을 다시 하는 경우는 연결이 끊기고 새로 저장이 된다.


함수의 인풋에서 많이 헷갈린다.


```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x = [4,5,6];
	x.push( 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [1,2,3,4]  not  [4,5,6,7]
```
a 를 전달하고 값을 바꾸면 반영이 되지만 중간에 =로 다른 배열을 지정하는 경우는 기존 연결과는 끊긴다. 그래서 그 이전 연결까지가 함수 리턴후에 유지된다.

```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x.length = 0; // empty existing array in-place
	x.push( 4, 5, 6, 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [4,5,6,7]  not  [1,2,3,4]
```

위와 같이 새로운 배열이 아니라 기존 값을 0으로 만들고 다시 푸쉬해서 반영 되도록 할 수 있다.

```js
foo( a.slice() );
```

`slice(..)` 는 값만 copy 되도록 할 수 있음.
