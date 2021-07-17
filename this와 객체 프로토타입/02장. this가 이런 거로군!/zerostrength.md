# 2. this가 이런 거로군! this All Makes Sense Now!

## 2.1. 호출부 (Call-site)

A함수 → B함수 → C함수 호출되는 경우 호출부 또한 계속 변경된다. 크롬 개발자 도구 break를 통해 라인별로 파악 가능하다.

## 2.2. 단지 규칙일 뿐 (Nothing but rules)

### 2.2.1. 기본 바인딩

단독 함수 실행 (Standalone Function Invocation)

```jsx
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2
```

### 2.2.2. 암시적 바인딩

```jsx
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

obj.foo(); // 2
```

**암시적 소실**

this 바인딩이 뜻밖에 헷갈리기 쉬운 경우

전역 객체나 undefined 중 한 가지로 기본 바인딩 된다.

### 2.2.3. 명시적 바인딩

```jsx
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

**하드 바인딩** (명시적 바인딩을 변형한 꼼수)

- 명시적이고 강력해서 하드 바인딩이라고 한다.

```jsx
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

var bar = function() {
	foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// `bar` hard binds `foo`'s `this` to `obj`
// so that it cannot be overriden
bar.call( window ); // 2
```

### 2.2.4. new 바인딩

함수 앞에 new를 붙이면

1. 새 객체가 툭 만들어진다.
2. 새로 생성된 객체의 프로토타입이 연결된다.
3. 새로 생성된 객체는 함수 호출 시 this로 바인딩 된다.
4. 이 함수가 자신의 또 다른 객체를 반환하지 않는 한 new와 함께 호출된 함수는 자동으로 새로 생성된 객체를 반환한다.

```jsx
function foo(a) {
	this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```

## 2.3. 모든 건 순서가 있는 법 (Everything In Order)

어느쪽이 우선일까?

명시적 바인딩이 암시적 바인딩보다 우선순위가 높다.

명시적 바인딩의 한 형태인 하드 코딩이 new 바인딩보다 우선순위가 높고 new로 오버라이드할 수 없다는 사실을 짐작할 수 있다.

```jsx
function foo(something) {
	this.a = something;
}

var obj1 = {
	foo: foo
};

var obj2 = {};

obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```

ES5에 내장된 Function.prototype.bind( ) 

굳이 new로 하드 바인딩을 오버라이드하려는 이유는 뭘까?

- this 하드 바인딩을 무시하는 함수를 생성하여 함수 인자를 전부 또는 일부만 미리 세팅 해야 할 때 유용하다. 원 함수의 기본 인자로 고정하는 역할을 한다. (partial application, Currying의 일종)

```jsx
function foo(p1,p2) {
	this.val = p1 + p2;
}

// using `null` here because we don't care about
// the `this` hard-binding in this scenario, and
// it will be overridden by the `new` call anyway!
var bar = foo.bind( null, "p1" );

var baz = new bar( "p2" );

baz.val; // p1p2
```

### 2.3.1. this 확정 규칙

1. new로 함수를 호출했는가? → 맞으면 새로 생성된 객체가 this다.

    ```jsx
    var bar = new foo();
    ```

2. call과 apply로 함수를 호출(명시적 바인딩), bind 하드 바인딩 내부에 숨겨진 형태로 호출됐는가? → 맞으면 명시적으로 지정된 객체가 this다.

    ```jsx
    var bar = foo.call(obj2);
    ```

3. 함수 콘텍스트(암시적 바인딩), 즉 객체를 소유 또는 포함하는 형태로 호출했는가? → 맞으면 바로 이 콘텍스트 객체가 this다.

    ```jsx
    var bar = obj1.foo();
    ```

4. 그 외의 경우에 this는 기본값(엄격모드에서는 undefined, 비엄격모드는 전역 객체)으로 세팅된다(기본 바인딩)

    ```jsx
    var bar = foo();
    ```

## 2.4. 바인딩 예외

위의 규칙에서의 예외

### 2.4.1. this 무시

call, apply, bind 메써드 첫 번째 인자로 null, undefined 넣으면 this 바인딩이 무시되고 기본 바인딩 규칙이 적용된다.

→ array spread에 사용용도로 쓰임 (?)

더 안전한 this

DMZ객체로 ø = Object.create(null)을 넣어준다.

### 2.4.2. 간접 레퍼런스 (Indirect References)

할당문에서 발생

```jsx
function foo() {
	console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

### 2.4.3. 소프트바인딩

- 소프트바인딩이라는 유틸리티
- 수동 바인딩을 할 수 있고 기본 바인딩 규칙이 적용되어야 할 땐 다시 obj로 되돌린다.

```jsx
if (!Function.prototype.softBind) {
	Function.prototype.softBind = function(obj) {
		var fn = this,
			curried = [].slice.call( arguments, 1 ),
			bound = function bound() {
				return fn.apply(
					(!this ||
						(typeof window !== "undefined" &&
							this === window) ||
						(typeof global !== "undefined" &&
							this === global)
					) ? obj : this,
					curried.concat.apply( curried, arguments )
				);
			};
		bound.prototype = Object.create( fn.prototype );
		return bound;
	};
}
```

```jsx
function foo() {
   console.log("name: " + this.name);
}

var obj = { name: "obj" },
    obj2 = { name: "obj2" },
    obj3 = { name: "obj3" };

var fooOBJ = foo.softBind( obj );

fooOBJ(); // name: obj

obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2   <---- look!!!

fooOBJ.call( obj3 ); // name: obj3   <---- look!

setTimeout( obj2.foo, 10 ); // name: obj   <---- falls back to soft-binding
```

## 2.5. 어휘적 this (Lexical this)

ES6에서 이 규칙을 따르지않는 특별한 함수. Arrow function

에두른 스코프(Enclosing scope)를 보고 this를 알아서 바인딩 한다.

그래서 화살표 함수는 이벤트 처리기나 타이머 등의 콜백에 널리 쓰인다.

```jsx
function foo() {
	// return an arrow function
	return (a) => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, not 3!
```

기본 ES6이전에는

```jsx
function foo() {
	var self = this; // lexical capture of `this`
	setTimeout( function(){
		console.log( self.a );
	}, 100 );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```