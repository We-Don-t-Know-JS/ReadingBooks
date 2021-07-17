# Chapter 5: Scope Closure

We arrive at this point with hopefully a very healthy, solid understanding of how scope works.

We turn our attention to an incredibly important, but persistently elusive(피하는, 알기 어려운), *almost mythological*, part of the language: **closure**

## Enlightenment

C**losure is all around you in JavaScript, you just have to recognize and embrace it.** No, closures are not even a weapon that you must learn to wield and master as Luke trained in The Force.

**Closures happen as a result of writing code that relies on lexical scope.** They just happen. You do not even really have to intentionally create closures to take advantage of them. Closures are created and used for you all over your code. What you are *missing* is the proper mental context to recognize, embrace, and leverage closures for your own will.

The enlightenment moment should be: **oh, closures are already occurring all over my code, I can finally *see* them now.** Understanding closures is like when Neo sees the Matrix for the first time.

## Nitty Gritty

> 클로저는 함수가 기억하고 렉시컬 스코프 밖에서 접근 가능할 때를 말한다.

```jsx
function foo() {
	var a = 2;

	function bar() {
		console.log( a ); // 2
	}

	bar();
}

foo();

```

위의 코드는 a를 접근하는거 같은데 "클로저"일까?

아마 맞지만 클로저의 의미의 일부. bar은 a를 lexical 스코프로 찾는거니.

하지만, 이렇게 정의된 클로저는 직접적으로 관찰가능하지않다(directly observable).

아래의 코드가 클로저의 전체를 보여준다:

```jsx
function foo() {
	var a = 2;

	function bar() {
		console.log( a );
	}

	return bar;
}

var baz = foo();

baz(); // 2 -- Whoa, closure was just observed, man.

```

bar를 레퍼렌스로 가지고 있는 함수 오브젝트를 여기서 리턴한다

여기서 foo() 실행한거에 baz() 함수를 불렀다. 이러면 실제 함수 내부에 있는 bar를 부르게 된다.

`bar()` 가 실행되지만 확실히 선언된 렉시컬 스코프 외부에 있다.

After `foo()` 실행이 끝나면 그 내부에 있는 스코프가 전부 다 사라질거라 기대한다.자바스크립트 엔진이 사용되지 않는것은 가비지 컬렉터가 알아서 메모리 free하니.

`foo()` 가 더 이상 사용되지 않아 사라지는게 맞을거같지만 클로저에서는 내부 스코프가 "사용중"이기 때문에 사라지지 않음. 그 대상은 **함수 `bar()` 자체**

**`bar()` 은 계속 그 스코프의 레퍼렌스를 가지고 있다, 그 레퍼렌스를 클로저라고 한다.**

함수는 author-time 렉시컬 스코프 외부에서도 잘 불려진다. **클로저**는 함수가 렉시컬 스코프에 계속 접근 할 수 있게 해준다.

여러 방식으로 전달되는 함수는 다 observing/exercising 클로저의 예다.

```jsx
function foo() {
	var a = 2;

	function baz() {
		console.log( a ); // 2
	}

	bar( baz );
}

function bar(fn) {
	fn(); // look ma, I saw closure!
}

```

return으로 함수전달이 아니라 함수를 부르는 것으로도 가능하다.

함수의 전달은 간접적으로도 가능하다.

```jsx
var fn;

function foo() {
	var a = 2;

	function baz() {
		console.log( a );
	}

	fn = baz; // assign `baz` to global variable
}

function bar() {
	fn(); // look ma, I saw closure!
}

foo();

bar(); // 2

```

어떻게 전달하든 스코프 레퍼렌스는 유지가 된다. 선언된 곳 기준으로! 실행할때 그 클로저가 실행이 될 것.

## Now I Can See

위의 예제들은 아카데믹한 내용들이라면 아래의 내용은 현실에서도 이미 클로저를 사용하고 있다는 예시이다.

```jsx
function wait(message) {

	setTimeout( function timer(){
		console.log( message );
	}, 1000 );

}

wait( "Hello, closure!" );

```

timer가 wait 스코프 위에 스코프 클로저를 가진다. message의 레퍼렌스도 가지며.

**Closure.**

jQuery의 예제:

```jsx
function setupBot(name,selector) {
	$( selector ).click( function activator(){
		console.log( "Activating: " + name );
	} );
}

setupBot( "Closure Bot 1", "#bot_1" );
setupBot( "Closure Bot 2", "#bot_2" );

```

함수 자체를 넘기는 경우에는 게속 클로저를 실행하게 된다.

Be that timers, event handlers, Ajax requests, cross-window messaging, web workers, or any of the other asynchronous (or synchronous!) tasks, when you pass in a *callback function*, get ready to sling some closure around!

**Note:** 챕터3에서 즉시 실행 함수 패턴을 배웠는데 IIFE 자체가 관찰가능한 클로저의 예이다.

```jsx
var a = 2;

(function IIFE(){
	console.log( a );
})();

```

This code "works", but it's not strictly an observation of closure. Why? Because the function (which we named "IIFE" here) is not executed outside its lexical scope. It's still invoked right there in the same scope as it was declared (the enclosing/global scope that also holds `a`). `a` is found via normal lexical scope look-up, not really via closure.

While closure might technically be happening at declaration time, it is *not* strictly observable, and so, as they say, *it's a tree falling in the forest with no one around to hear it.*

Though an IIFE is not *itself* an example of closure, it absolutely creates scope, and it's one of the most common tools we use to create scope which can be closed over. So IIFEs are indeed heavily related to closure, even if not exercising closure themselves.

Put this book down right now, dear reader. I have a task for you. Go open up some of your recent JavaScript code. Look for your functions-as-values and identify where you are already using closure and maybe didn't even know it before.

I'll wait.

Now... you see!

## Loops + Closure

for-loop에서 클로저가 관여하는 흔한 예제

```jsx
for (var i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}

```

**Note:** 린트는 반복문 안에 함수를 넣으면 안된다고 말한다. 클로저의 이해가 없이 사용하며 실수하는 경우가 많기 때문이다.

위의 코드는 1,2,3,4,5가 출력될것 같지만, 실제로는 6이 5번 출력된다. 1초 간격으로.

**Huh?**

Firstly, let's explain where `6` comes from. The terminating condition of the loop is when `i` is *not* `<=5`. The first time that's the case is when `i` is 6. So, the output is reflecting the final value of the `i` after the loop terminates.

This actually seems obvious on second glance. The timeout function callbacks are all running well after the completion of the loop. In fact, as timers go, even if it was `setTimeout(.., 0)` on each iteration, all those function callbacks would still run strictly after the completion of the loop, and thus print `6` each time.

But there's a deeper question at play here. What's *missing* from our code to actually have it behave as we semantically have implied?

What's missing is that we are trying to *imply* that each iteration of the loop "captures" its own copy of `i`, at the time of the iteration. But, the way scope works, all 5 of those functions, though they are defined separately in each loop iteration, all **are closed over the same shared global scope**, which has, in fact, only one `i` in it.

Put that way, *of course* all functions share a reference to the same `i`. Something about the loop structure tends to confuse us into thinking there's something else more sophisticated at work. There is not. There's no difference than if each of the 5 timeout callbacks were just declared one right after the other, with no loop at all.

고치는 방법으로는, 각 반복문마다 새로운 클로저 스코프가 필요하다.

챕터 3에서 배운것처럼 IIFE는 선언하자마자 실행한다.

Let's try:

```jsx
for (var i=1; i<=5; i++) {
	(function(){
		setTimeout( function timer(){
			console.log( i );
		}, i*1000 );
	})();
}

```

위의 방법도 실행하면 결과는 동일하다. 6을 5번 실행.

정상적일것 같지만 문제가 되는 이유는 **스코프가 비어있기 때문**. IIFE는 아무것도 하지않는 스코프라 의미있는 것을 넣어줘야한다.

변수를 새로 만들어서 i의 카피값을 각 iteration에 가지고 있어야 한다.

```jsx
for (var i=1; i<=5; i++) {
	(function(){
		var j = i;
		setTimeout( function timer(){
			console.log( j );
		}, j*1000 );
	})();
}

```

**드디어 원하는 결과물이 나온다!**

조금 변형하자면:

```jsx
for (var i=1; i<=5; i++) {
	(function(j){
		setTimeout( function timer(){
			console.log( j );
		}, j*1000 );
	})( i );
}

```

새로운 변수 지정이 아니라 함수에 j를 인자로 넣고 i를 넘겨주면 된다.

이제 각 iteration 마다 새로운 스코프가 생성되고 setTimeout function callback도 새로운 스코프를 가질 기회를 얻음.

### Block Scoping Revisited

Look carefully at our analysis of the previous solution. We used an IIFE to create new scope per-iteration. In other words, we actually *needed* a per-iteration **block scope**. Chapter 3 showed us the `let` declaration, which hijacks a block and declares a variable right there in the block.

**It essentially turns a block into a scope that we can close over.** So, the following awesome code "just works":

```jsx
for (var i=1; i<=5; i++) {
	let j = i; // yay, block-scope for closure!
	setTimeout( function timer(){
		console.log( j );
	}, j*1000 );
}

```

*But, that's not all!* (in my best Bob Barker voice). There's a special behavior defined for `let` declarations used in the head of a for-loop. This behavior says that the variable will be declared not just once for the loop, **but each iteration**. And, it will, helpfully, be initialized at each subsequent iteration with the value from the end of the previous iteration.

```jsx
for (let i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}

```

How cool is that? Block scoping and closure working hand-in-hand, solving all the world's problems. I don't know about you, but that makes me a happy JavaScripter.

## Modules

**클로저**의 힘은 콜백뿐만 아니라 *모듈*에서도 사용된다.

```jsx
function foo() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}
}

```

private 변수는 `something` 와 `another` 두개 있고 내부 함수 `doSomething()` 과 `doAnother()`

```jsx
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3

```

자바스크립트에서 이런 패턴은 모듈이라고 한다.

이런 모듈 패턴을 "숨기는 모듈"

 1.  `CoolModule()` 은 함수, 실행이 되어야만 내부 스코프 등이 생성된다.

 2.  `CoolModule()` 함수는 object를 리턴한다, denoted by the object-literal syntax `{ key: value, ... }`. 내부 변수들은 숨겨져있고 private하다. 리턴 오브젝트값이 **모듈의 public API**라 생각하면 된다.

리턴값은 `foo`밖에서 사용 가능, 그리고 외부에서 접근 가능하다 `foo.doSomething()` 같이

**Note:** 실제 object를 꼭 리턴 할 필요는 없다. 내부 함수를 바로 리턴해도 괜찮다. jQuery가 좋은 예다. `jQuery` 와 `$` 는 jQuery 모듈의 public API이다. 그 자체는 또한 함수.

The `doSomething()` and `doAnother()` functions have closure over the inner scope of the module "instance" (arrived at by actually invoking `CoolModule()`). When we transport those functions outside of the lexical scope, by way of property references on the object we return, we have now set up a condition by which closure can be observed and exercised.

간단히, 모듈 패턴이 성립하기 위해 2가지가 필요하다:

1. outer enclosing 함수가 있어야한다. 최소 한번은 실행되어야 한다.
2. enclosing 함수는 적어도 하나의 내부 함수를 리턴 해야한다. 그래야 내부 함수가 private 스코프에 접근 가능하다.

An object with a function property on it alone is not *really* a module. An object which is returned from a function invocation which only has data properties on it and no closured functions is not *really* a module, in the observable sense.

위의 예제에서 `CoolModule()` 는 언제나 수많이 생성 가능하다. 하나만 생성하고 싶다 "singleton"의 패턴을 사용하면 된다:

```jsx
var foo = (function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3

```

여기서 모듈 함수를 IIFE로 만들었다. 바로 실행을 하고 리턴하여 foo라는 모듈이 사용 가능하다.

모듈은 함수이다. 인자를 받을 수 있다:

```jsx
function CoolModule(id) {
	function identify() {
		console.log( id );
	}

	return {
		identify: identify
	};
}

var foo1 = CoolModule( "foo 1" );
var foo2 = CoolModule( "foo 2" );

foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"

```

Public API 용도로 리턴도 가능:

```jsx
var foo = (function CoolModule(id) {
	function change() {
		// modifying the public API
		publicAPI.identify = identify2;
	}

	function identify1() {
		console.log( id );
	}

	function identify2() {
		console.log( id.toUpperCase() );
	}

	var publicAPI = {
		change: change,
		identify: identify1
	};

	return publicAPI;
})( "foo module" );

foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE

```

### 모던 모듈

Various module dependency loaders/managers essentially wrap up this pattern of module definition into a friendly API. Rather than examine any one particular library, let me present a *very simple* proof of concept **for illustration purposes (only)**:

```jsx
var MyModules = (function Manager() {
	var modules = {};

	function define(name, deps, impl) {
		for (var i=0; i<deps.length; i++) {
			deps[i] = modules[deps[i]];
		}
		**modules[name] = impl.apply( impl, deps );**
	}

	function get(name) {
		return modules[name];
	}

	return {
		define: define,
		get: get
	};
})();

```

The key part of this code is `modules[name] = impl.apply(impl, deps)`. This is invoking the definition wrapper function for a module (passing in any dependencies), and storing the return value, the module's API, into an internal list of modules tracked by name.

이걸 통해서 아래와 같이 모듈 정의 가능하다:

```jsx
MyModules.define( "bar", [], function(){
	function hello(who) {
		return "Let me introduce: " + who;
	}

	return {
		hello: hello
	};
} );

MyModules.define( "foo", ["bar"], function(bar){
	var hungry = "hippo";

	function awesome() {
		console.log( bar.hello( hungry ).toUpperCase() );
	}

	return {
		awesome: awesome
	};
} );

var bar = MyModules.get( "bar" );
var foo = MyModules.get( "foo" );

console.log(
	bar.hello( "hippo" )
); // Let me introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO

```

"foo" 와 "bar" 모두 퍼블릭 API를 리턴한다. "foo" even receives the instance of "bar" as a dependency parameter, and can use it accordingly.

### 미래 모듈

ES6 는 모듈이라는 컨셉을 위해 first-class syntax를 추가했다. ES6 는 파일을 별도의 모듈로 취급한다. 각 모듈은 다른 모듈을 가져오거나 특정 API 멤버를 가져오고 내보낼수도 있다.

**Note:** 함수 기반 모듈은 정적으로 인식된 패턴이 아니라, API 시멘틱이 런타임 이전에는 알 수 없다. 즉, 런타임에서 모듈의 API 수정이 가능하다.

대조적으로 ES6 모듈 API는 정적이다. 런타임에 변하지 않는다. Since the compiler knows *that*, it can (and does!) check during (file loading and) compilation that a reference to a member of an imported module's API *actually exists*. API 레퍼렌스If the API reference doesn't exist, the compiler throws an "early" error at compile-time, rather than waiting for traditional dynamic run-time resolution (and errors, if any).

ES6 modules **do not** have an "inline" format, they must be defined in separate files (one per module). The browsers/engines have a default "module loader" (which is overridable, but that's well-beyond our discussion here) which synchronously loads a module file when it's imported.

Consider:

**bar.js**

```jsx
function hello(who) {
	return "Let me introduce: " + who;
}

export hello;

```

**foo.js**

```jsx
// import only `hello()` from the "bar" module
import hello from "bar";

var hungry = "hippo";

function awesome() {
	console.log(
		hello( hungry ).toUpperCase()
	);
}

export awesome;

```

```jsx
// import the entire "foo" and "bar" modules
module foo from "foo";
module bar from "bar";

console.log(
	bar.hello( "rhino" )
); // Let me introduce: rhino

foo.awesome(); // LET ME INTRODUCE: HIPPO

```

**Note:** Separate files **"foo.js"** and **"bar.js"** would need to be created, with the contents as shown in the first two snippets, respectively. Then, your program would load/import those modules to use them, as shown in the third snippet.

`import` imports one or more members from a module's API into the current scope, each to a bound variable (`hello` in our case). `module` imports an entire module API to a bound variable (`foo`, `bar` in our case). `export` exports an identifier (variable, function) to the public API for the current module. These operators can be used as many times in a module's definition as is necessary.

The contents inside the *module file* are treated as if enclosed in a scope closure, just like with the function-closure modules seen earlier.