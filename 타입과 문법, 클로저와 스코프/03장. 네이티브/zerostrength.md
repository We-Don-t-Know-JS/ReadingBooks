# You Don't Know JS: Types & Grammar
# Chapter 3: Natives

가장 많이 사용하는 natives 목록:

* `String()`
* `Number()`
* `Boolean()`
* `Array()`
* `Object()`
* `Function()`
* `RegExp()`
* `Date()`
* `Error()`
* `Symbol()` -- added in ES6!

모두 built-in 함수이다.

```js
var a = new String( "abc" );

typeof a; // "object" ... not "String"

a instanceof String; // true

Object.prototype.toString.call( a ); // "[object String]"
```

String에서 출력이 typeof, instanceof 결과가 다르다.

## Internal `[[Class]]`

자바스크립트 내부적으로 Class라는 것이 있는데 toString을 통해 확인이 가능하다.

```js
Object.prototype.toString.call( [1,2,3] );			// "[object Array]"

Object.prototype.toString.call( /regex-literal/i );	// "[object RegExp]"
```

```js
Object.prototype.toString.call( null );			// "[object Null]"
Object.prototype.toString.call( undefined );	// "[object Undefined]"
```

`string`, `number`, `boolean`는 boxing을 통해 변환이 된다.

```js
Object.prototype.toString.call( "abc" );	// "[object String]"
Object.prototype.toString.call( 42 );		// "[object Number]"
Object.prototype.toString.call( true );		// "[object Boolean]"
```

## Boxing Wrappers

자바스크립트는 알아서 래핑을 해주기 때문에 String( )으로 만들지 않아도 아래와 같이 사용 가능하다.

```js
var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"
```

미리 스트링으로 저장해주면 더 빨라지지않을까 싶지만 실제로는 그냥 자바스크립트 엔진히 하도록 놔두는것이 더 빠르다.

### Object Wrapper의 함정

Boolean으로 감싸면 그 값 자체가 true이기 때문에 함정에 빠진다.

```js
var a = new Boolean( false );

if (!a) {
	console.log( "Oops" ); // never runs
}
```

이와같이 a = false라고 하면 되는데 굳이 직접 박싱할 이유는 없다.


## Unboxing

반대로 원시값은 valueOf()로 가능하다:

```js
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

언박싱 또한, 암묵적으로 이루어진다.

```js
var a = new String( "abc" );
var b = a + ""; // `b` has the unboxed primitive value "abc"

typeof a; // "object"
typeof b; // "string"
```

## Natives as Constructors

생성자처럼 생성 가능

### `Array(..)`

```js
var a = new Array( 1, 2, 3 );
a; // [1, 2, 3]

var b = [1, 2, 3];
b; // [1, 2, 3]
```

**Note:** new가 있는것과 없는것 동일하다.

길이를 미리 정할 수도 있다.

```js
var a = new Array( 3 );

a.length; // 3
a;
```

배열안에 빈 값이 있다면 join을 할 때 결과물이 의도치 않게 나옴

```js
function fakeJoin(arr,connector) {
	var str = "";
	for (var i = 0; i < arr.length; i++) {
		if (i > 0) {
			str += connector;
		}
		if (arr[i] !== undefined) {
			str += arr[i];
		}
	}
	return str;
}

var a = new Array( 3 );
fakeJoin( a, "-" ); // "--"
```

진짜 undefined로 채워진 배열을 만들고 싶다면 apply 이용

```js
var a = Array.apply( null, { length: 3 } );
a; // [ undefined, undefined, undefined ]
```

### `Object(..)`, `Function(..)`, and `RegExp(..)`

굳이 사용할 이유 x

### `Date(..)` and `Error(..)`

에러를 주는 방식으로 Error 생성자 이용 가능하다.

```js
function foo(x) {
	if (!x) {
		throw new Error( "x wasn't provided" );
	}
	// ..
}
```

### `Symbol(..)`

ES6에서는 primitive value로 Symbol이 추가되었다.

Symbole은 앞에 new를 붙이면 유일하게 에러나는 생성자다.

Symbol.create, Symbol.iterator는 미리 정의되어 있다.

심벌은 객체가 아니라 단순 스칼라 값이다.


### Native Prototypes

내장 네이티브 생성자는 각자의 prototype 객체를 가진다. (`Array.prototype`, `String.prototype` 등)

String 객체는 기본적으로 String.prototype 객체에 정의된 메서드에 접근 가능하다.

**Note:** By documentation convention, `String.prototype.XYZ` is shortened to `String#XYZ`, and likewise for all the other `.prototype`s.

* `String#indexOf(..)`: find the position in the string of another substring
* `String#charAt(..)`: access the character at a position in the string
* `String#substr(..)`, `String#substring(..)`, and `String#slice(..)`: extract a portion of the string as a new string
* `String#toUpperCase()` and `String#toLowerCase()`: create a new string that's converted to either uppercase or lowercase
* `String#trim()`: create a new string that's stripped of any trailing or leading whitespace
