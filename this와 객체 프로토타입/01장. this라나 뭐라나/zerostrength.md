# Chapter 1. this라나 뭐라나 (this or that)

## 1.1. this를 왜?

암시적인 객체 레퍼런스를 '함께 넘기는 Passing Along' this 체계가 API 설계상 좀 더 깔끔하고 명확하며 재사용이 쉽다.

## 1.2. 헷갈리는 것들

### 1.2.1. 자기 자신

정의된 함수에서 this는 그 함수 자기 자신이라 생각하겠지만 틀렸다.

```jsx
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 0 -- WTF?
```

foo.count = 0을 하면 foo라는 함수 객체에 count 프로퍼티가 추가된다. 하지만 this.count에서 this는 함수 객체를 바라보는 것이 아니며, 프로퍼티 명이 똑같아 헷갈리지만 근거지를 둔 객체 자체가 다르다.

다른 변수를 통해 우회적으로 구현할 수는 있겠지만..

작가가 원하는 방식은 정확한 this를 이해하고 사용하는 방식

```jsx
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	// Note: `this` IS actually `foo` now, based on
	// how `foo` is called (see below)
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		// using `call(..)`, we ensure the `this`
		// points at the function object (`foo`) itself
		foo.call( foo, i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 4
```

### 1.2.2. 자신의 스코프

`연결 통로란 없다`

## 1.3. this는 무엇인가?

this는 작성 시점이 아닌 `런타임 시점`에 바인딩 되며 **함수 호출 당시 상황**에 따라 **컨텍스트**가 결정된다.

어떤 함수를 호출하면 활성화 레코드 (activation record), 즉 실행 콘텍스트(Execution context)가 만들어진다.

Call-stack과 호출 방법, 전달된 인자 등의 정보가 담겨있다.

2장은 this 바인딩을 결정짓는 함수 호출부(Call-Site)를 설명한다.