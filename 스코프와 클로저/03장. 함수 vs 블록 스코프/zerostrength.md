# Function vs Block Scope, Hoisting

# You Don't Know JS: Scope & Closures

# Chapter 3: Function vs. Block Scope

scope는 각각이 컨테이너 역할을 하는 버블의 연속이다. nesting은 author-time에 정의된다

어떤게 새로운 버블을 만드나? 함수만? 다른 구조체도 스코프 버블을 만들 수 있나?

## Scope From Functions

자바스크립트는 function-based scope이다.

함수 선언마다 그게 버블을 만든다.

```jsx
function foo(a) {
	var b = 2;

	// some code

	function bar() {
		// ...
	}

	// more code

	var c = 3;
}

```

`foo(..)` 의 코드 버블은 `a`, `b`, `c` , `bar` 을 포함한다.

스코프내에서 어디에 선언 되는지는 상관없고 그게 스코프 버블안에 속해 있는지가 중요

`bar(..)` 는 자신의 스코프 버블을 가지고 있다. 글로벌 스코프는 `foo` 를 포함하고 있다.

foo안에서 a,b,c가 정의되어 있으니 foo밖에서는 접귾ㄹ 수 없다.

```jsx
bar(); // fails

console.log( a, b, c ); // Reference Error !g
```

함수 스코프는 모든 변수가 함수에 속한다는 아이디어. 이러한 디자인은 유용하지만 주의를 하지않으면 예상치 못한 에러가 발생 할 수도 있다.

## Hiding In Plain Scope

함수안에 정의한 코드들은 숨겨지는 효과도 있다.

근데 왜 숨기는게 좋은 경우가..?

**Software design principle "Principle of Least Privilege"**

변수를 최소환으로 노출시키는

예시:

```jsx
function doSomething(a) {
	b = a + doSomethingElse( a * 2 );

	console.log( b * 3 );
}

function doSomethingElse(a) {
	return a - 1;
}

var b;

doSomething( 2 ); // 15

```

In this snippet, the `b` variable and the `doSomethingElse(..)` function are likely "private" details of how `doSomething(..)` does its job. Giving the enclosing scope "access" to `b` and `doSomethingElse(..)` is not only unnecessary but also possibly "dangerous", in that they may be used in unexpected ways, intentionally or not, and this may violate pre-condition assumptions of `doSomething(..)`.

A more "proper" design would hide these private details inside the scope of `doSomething(..)`, such as:

```jsx
function doSomething(a) {
	function doSomethingElse(a) {
		return a - 1;
	}

	var b;

	b = a + doSomethingElse( a * 2 );

	console.log( b * 3 );
}

doSomething( 2 ); // 15

```

이러면 `b` 와 `doSomethingElse(..)` 는 다른 밖의 영향을 받지 않고 `doSomething(...)` 의 영향만 받는다.

### 충돌 방지

"숨기는" 장점은 의도하지 않은 충돌을 방지할수 있다는 것.

충돌은 자주 의도치 않은 덮어쓰기에서 발생한다.

For example:

```jsx
function foo() {
	function bar(a) {
		i = 3; // changing the `i` in the enclosing scope's for-loop
		console.log( a + i );
	}

	for (var i=0; i<10; i++) {
		bar( i * 2 ); // oops, infinite loop ahead!
	}
}

foo();

```

### Global "Namespaces"

변수 충돌은 글로벌 스코프에서 발생한다.

여러 라이브러리를 로드하면 잘 숨기지 않았다면 서로 충돌 할 수가 있다.

라이브러리에서는 보통 한 변수로 선언하고 글로벌 스코프에서 네임스페이스로 사용된다.

예:

```jsx
var MyReallyCoolLibrary = {
	awesome: "stuff",
	doSomething: function() {
		// ...
	},
	doAnotherThing: function() {
		// ...
	}
};

```

### 모듈 관리

다른 방법으로 현대 "module" 접근을 사용, 여러 의존 매니저를 통해.

이걸 사용하면 라이브러리에서 글로벌스코프에 정의되는 identifiers가 없음. 대신 그 identifiers를 import 해서 사용해야함.

## Functions As Scopes

For example:

```jsx
var a = 2;

function foo() { // <-- insert this

	var a = 3;
	console.log( a ); // 3

} // <-- and this
foo(); // <-- and this

console.log( a ); // 2

```

위의 방법도 작동하지만 이상적이지는 않다.

1. foo라는 이름의 함수를 선언해야한다. 글로벌 스코프에 영향을 준다.
2. 다시 함수를 콜하는 것도 작성을 해줘야 실행이 된다.

함수가 이름이 없거나 자동으로 실행된다면 더 이상적일 것. 자바스크립트는 둘다 제공한다.

```jsx
var a = 2;

(function foo(){ // <-- insert this

	var a = 3;
	console.log( a ); // 3

})(); // <-- and this

console.log( a ); // 2

```

이렇게 (function 을하면 이 함수는 function-expression이 된다.

**Note:** declaration vs. expression와의 구분하기 쉬운방법은 단어 "function"의 위치이다. statement에서 function 단어로 시작하면 declaration이고 아니면 function expression이다.

다른점은 그 이름이 identifier로 되어있는지의 유무.

`(function foo(){ .. })` 에서 foo는 그 범위내에서만 정의되어있고 밖의 스코프는 아니다. foo를 안에 숨김으로써 불필요한 오염을 하지 않는다.

### Anonymous vs. Named

아래와 같은 콜백 parameter로 함수 표현 넣는거에 익숙할것이다.

```jsx
setTimeout( function(){
	console.log("I waited 1 second!");
}, 1000 );
```

이건 익명 함수 표현식이라고 한다. 이름이 안붙여져있기 때문.

함수 표현은 익명이 가능하지만 선언은 불가능하다.

익명함수는 빠르고 쉽게 가능하고 이러한 코드 스타일도 장려한다. 하지만 몇가지 고려해야하는 단점이 있다:

1. 이름이 없기 때문에 디버깅에 어려움이 있다.
2. 이름이 없기 때문에 재귀함수의 경우 본인을 직접 불러야한다. **deprecated** `arguments.callee` 를 사용해야 한다.
3. 익명 함수는 간결해서 더 이해하기 쉬운 코드이지만 설명이되어있는 이름도 도움을 준다.

**Inline function expressions** 는 강력하고 효과적이다 -- the question of anonymous vs. named doesn't detract from that. 위의 단점을 커버하기 위해서는 이름만 넣어주면 다 해결되고 단점도 없다. 아래와 같이 항상 이름을 함수 표현식에도 넣어주면 좋다:

```jsx
setTimeout( function timeoutHandler(){ // <-- Look, I have a name!
	console.log( "I waited 1 second!" );
}, 1000 );

```

### Invoking Function Expressions Immediately

```jsx
var a = 2;

(function foo(){

	var a = 3;
	console.log( a ); // 3

})();

console.log( a ); // 2

```

이 패턴은 널리쓰이고 즉시 실행 함수로 정의 되었다.

Of course, IIFE's don't need names, necessarily -- the most common form of IIFE is to use an anonymous function expression. While certainly less common, naming an IIFE has all the aforementioned benefits over anonymous function expressions, so it's a good practice to adopt.

```jsx
var a = 2;

(function IIFE(){

	var a = 3;
	console.log( a ); // 3

})();

console.log( a ); // 2

```

There's a slight variation on the traditional IIFE form, which some prefer: `(function(){ .. }())`. Look closely to see the difference. In the first form, the function expression is wrapped in `( )`, and then the invoking `()` pair is on the outside right after it. In the second form, the invoking `()` pair is moved to the inside of the outer `( )` wrapping pair.

These two forms are identical in functionality. **It's purely a stylistic choice which you prefer.**

Another variation on IIFE's which is quite common is to use the fact that they are, in fact, just function calls, and pass in argument(s).

For instance:

```jsx
var a = 2;

(function IIFE( global ){

	var a = 3;
	console.log( a ); // 3
	console.log( global.a ); // 2

})( window );

console.log( a ); // 2

```

We pass in the `window` object reference, but we name the parameter `global`, so that we have a clear stylistic delineation for global vs. non-global references. Of course, you can pass in anything from an enclosing scope you want, and you can name the parameter(s) anything that suits you. This is mostly just stylistic choice.

Another application of this pattern addresses the (minor niche) concern that the default `undefined` identifier might have its value incorrectly overwritten, causing unexpected results. By naming a parameter `undefined`, but not passing any value for that argument, we can guarantee that the `undefined` identifier is in fact the undefined value in a block of code:

```jsx
undefined = true; // setting a land-mine for other code! avoid!

(function IIFE( undefined ){

	var a;
	if (a === undefined) {
		console.log( "Undefined is safe here!" );
	}

})();

```

Still another variation of the IIFE inverts the order of things, where the function to execute is given second, *after* the invocation and parameters to pass to it. This pattern is used in the UMD (Universal Module Definition) project. Some people find it a little cleaner to understand, though it is slightly more verbose.

```jsx
var a = 2;

(function IIFE( def ){
	def( window );
})(function def( global ){

	var a = 3;
	console.log( a ); // 3
	console.log( global.a ); // 2

});

```

The `def` function expression is defined in the second-half of the snippet, and then passed as a parameter (also called `def`) to the `IIFE` function defined in the first half of the snippet. Finally, the parameter `def` (the function) is invoked, passing `window` in as the `global` parameter.

## Blocks As Scopes

블록 스코프는 몰라도 아래 코드는 익숙할것

```jsx
for (var i=0; i<10; i++) {
	console.log( i );
}

```

We declare the variable `i` directly inside the for-loop head, most likely because our *intent* is to use `i` only within the context of that for-loop, and essentially ignore the fact that the variable actually scopes itself to the enclosing scope (function or global).

That's what block-scoping is all about. Declaring variables as close as possible, as local as possible, to where they will be used. Another example:

```jsx
var foo = true;

if (foo) {
	var bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}

```

We are using a `bar` variable only in the context of the if-statement, so it makes a kind of sense that we would declare it inside the if-block. However, where we declare variables is not relevant when using `var`, because they will always belong to the enclosing scope. This snippet is essentially "fake" block-scoping, for stylistic reasons, and relying on self-enforcement not to accidentally use `bar` in another place in that scope.

Block scope is a tool to extend the earlier "Principle of Least ~~Privilege~~ Exposure" [^note-leastprivilege] from hiding information in functions to hiding information in blocks of our code.

Consider the for-loop example again:

```jsx
for (var i=0; i<10; i++) {
	console.log( i );
}

```

Why pollute the entire scope of a function with the `i` variable that is only going to be (or only *should be*, at least) used for the for-loop?

But more importantly, developers may prefer to *check* themselves against accidentally (re)using variables outside of their intended purpose, such as being issued an error about an unknown variable if you try to use it in the wrong place. Block-scoping (if it were possible) for the `i` variable would make `i` available only for the for-loop, causing an error if `i` is accessed elsewhere in the function. This helps ensure variables are not re-used in confusing or hard-to-maintain ways.

But, the sad reality is that, on the surface, JavaScript has no facility for block scope.

That is, until you dig a little further.

### `with`

챕터 2에서 with를 배웠고 이것도 하나의 block scope의 예이다.

### `try/catch`

It's a *very* little known fact that JavaScript in ES3 specified the variable declaration in the `catch` clause of a `try/catch` to be block-scoped to the `catch` block.

For instance:

```jsx
try {
	undefined(); // illegal operation to force an exception!
}
catch (err) {
	console.log( err ); // works!
}

console.log( err ); // ReferenceError: `err` not found

```

As you can see, `err` exists only in the `catch` clause, and throws an error when you try to reference it elsewhere.

**Note:** While this behavior has been specified and true of practically all standard JS environments (except perhaps old IE), many linters seem to still complain if you have two or more `catch` clauses in the same scope which each declare their error variable with the same identifier name. This is not actually a re-definition, since the variables are safely block-scoped, but the linters still seem to, annoyingly, complain about this fact.

To avoid these unnecessary warnings, some devs will name their `catch` variables `err1`, `err2`, etc. Other devs will simply turn off the linting check for duplicate variable names.

The block-scoping nature of `catch` may seem like a useless academic fact, but see Appendix B for more information on just how useful it might be.

### `let`

Thus far, we've seen that JavaScript only has some strange niche behaviors which expose block scope functionality. If that were all we had, and *it was* for many, many years, then block scoping would not be terribly useful to the JavaScript developer.

Fortunately, ES6 changes that, and introduces a new keyword `let` which sits alongside `var` as another way to declare variables.

The `let` keyword attaches the variable declaration to the scope of whatever block (commonly a `{ .. }` pair) it's contained in. In other words, `let` implicitly hijacks any block's scope for its variable declaration.

```jsx
var foo = true;

if (foo) {
	let bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}

console.log( bar ); // ReferenceError

```

Using `let` to attach a variable to an existing block is somewhat implicit. It can confuse you if you're not paying close attention to which blocks have variables scoped to them, and are in the habit of moving blocks around, wrapping them in other blocks, etc., as you develop and evolve code.

Creating explicit blocks for block-scoping can address some of these concerns, making it more obvious where variables are attached and not. Usually, explicit code is preferable over implicit or subtle code. This explicit block-scoping style is easy to achieve, and fits more naturally with how block-scoping works in other languages:

```jsx
var foo = true;

if (foo) {
	{ // <-- explicit block
		let bar = foo * 2;
		bar = something( bar );
		console.log( bar );
	}
}

console.log( bar ); // ReferenceError

```

We can create an arbitrary block for `let` to bind to by simply including a `{ .. }` pair anywhere a statement is valid grammar. In this case, we've made an explicit block *inside* the if-statement, which may be easier as a whole block to move around later in refactoring, without affecting the position and semantics of the enclosing if-statement.

**Note:** For another way to express explicit block scopes, see Appendix B.

In Chapter 4, we will address hoisting, which talks about declarations being taken as existing for the entire scope in which they occur.

However, declarations made with `let` will *not* hoist to the entire scope of the block they appear in. Such declarations will not observably "exist" in the block until the declaration statement.

```jsx
{
   console.log( bar ); // ReferenceError!
   let bar = 2;
}

```

### Garbage Collection

블록 스코핑의 장점은 메모리 다시 확보하는것.

Consider:

```jsx
function process(data) {
	// do something interesting
}

var someReallyBigData = { .. };

process( someReallyBigData );

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, /*capturingPhase=*/false );

```

클릭 함수는 많은데이터 가진 변수가 필요 없지만 js엔진은 계속 가지고 있다. 아래와 같이 로컬 블럭으로 가비지 컬렉션 가능

.

```jsx
function process(data) {
	// do something interesting
}

// anything declared inside this block can go away after!
{
	let someReallyBigData = { .. };

	process( someReallyBigData );
}

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, /*capturingPhase=*/false );

```

### `let` Loops

Let이 좋은 이유는 이전에 말한 for loop 케이스이다.

```jsx
for (let i=0; i<10; i++) {
	console.log( i );
}

console.log( i ); // ReferenceError

```

헤더에서 i에 바인드하는것뿐 아니라 루프 각 iteration에서 re-bind한다.

```jsx
{
	let j;
	for (j=0; j<10; j++) {
		let i = j; // re-bound for each iteration!
		console.log( i );
	}
}

```

The reason why this per-iteration binding is interesting will become clear in Chapter 5 when we discuss closures.

Because `let` declarations attach to arbitrary blocks rather than to the enclosing function's scope (or global), there can be gotchas where existing code has a hidden reliance on function-scoped `var` declarations, and replacing the `var` with `let` may require additional care when refactoring code.

Consider:

```jsx
var foo = true, baz = 10;

if (foo) {
	var bar = 3;

	if (baz > bar) {
		console.log( baz );
	}

	// ...
}

```

This code is fairly easily re-factored as:

```jsx
var foo = true, baz = 10;

if (foo) {
	var bar = 3;

	// ...
}

if (baz > bar) {
	console.log( baz );
}

```

But, be careful of such changes when using block-scoped variables:

```jsx
var foo = true, baz = 10;

if (foo) {
	let bar = 3;

	if (baz > bar) { // <-- don't forget `bar` when moving!
		console.log( baz );
	}
}

```

See Appendix B for an alternate (more explicit) style of block-scoping which may provide easier to maintain/refactor code that's more robust to these scenarios.

### `const`

블록 스코프를 만드는 let과 함께 const도 있다.

값은 고정이라 나중에 수정하려하면 오류난다.

```jsx
var foo = true;

if (foo) {
	var a = 2;
	const b = 3; // block-scoped to the containing `if`

	a = 3; // just fine!
	b = 4; // error!
}

console.log( a ); // 3
console.log( b ); // ReferenceError!

```

# Chapter 4: Hoisting

Function scope과 block scope 전부 동일하게 안에서 정의된 변수는 그 범위내에 붙는다.

그러나 조금씩 다른게 있어서 이 장에서 다룬다.

## Chicken Or The Egg?

자바스크립트 코드가 라인 바이 라인이라 생각하기 쉽다. 위에서 아래로.

Consider this code:

```jsx
a = 2;

var a;

console.log( a );

```

여기에서 대부분 undefined 예상하겠지만 실제로는 2가 출력된다.

Consider another piece of code:

```jsx
console.log( a );

var a = 2;

```

You might be tempted to assume that, since the previous snippet exhibited some less-than-top-down looking behavior, perhaps in this snippet, `2` will also be printed. Others may think that since the `a` variable is used before it is declared, this must result in a `ReferenceError` being thrown.

Unfortunately, both guesses are incorrect. `undefined` is the output.

**So, what's going on here?** It would appear we have a chicken-and-the-egg question. Which comes first, the declaration ("egg"), or the assignment ("chicken")?

## The Compiler Strikes Again

To answer this question, we need to refer back to Chapter 1, and our discussion of compilers. Recall that the *Engine* actually will compile your JavaScript code before it interprets it. Part of the compilation phase was to find and associate all declarations with their appropriate scopes. Chapter 2 showed us that this is the heart of Lexical Scope.

So, the best way to think about things is that all declarations, both variables and functions, are processed first, before any part of your code is executed.

When you see `var a = 2;`, you probably think of that as one statement. But JavaScript actually thinks of it as two statements: `var a;` and `a = 2;`. The first statement, the declaration, is processed during the compilation phase. The second statement, the assignment, is left **in place** for the execution phase.

Our first snippet then should be thought of as being handled like this:

```jsx
var a;

```

```jsx
a = 2;

console.log( a );

```

...where the first part is the compilation and the second part is the execution.

Similarly, our second snippet is actually processed as:

```jsx
var a;

```

```jsx
console.log( a );

a = 2;

```

So, one way of thinking, sort of metaphorically, about this process, is that variable and function declarations are "moved" from where they appear in the flow of the code to the top of the code. This gives rise to the name "Hoisting".

In other words, **the egg (declaration) comes before the chicken (assignment)**.

**Note:** Only the declarations themselves are hoisted, while any assignments or other executable logic are left *in place*. If hoisting were to re-arrange the executable logic of our code, that could wreak havoc.

```jsx
foo();

function foo() {
	console.log( a ); // undefined

	var a = 2;
}

```

The function `foo`'s declaration (which in this case *includes* the implied value of it as an actual function) is hoisted, such that the call on the first line is able to execute.

It's also important to note that hoisting is **per-scope**. So while our previous snippets were simplified in that they only included global scope, the `foo(..)` function we are now examining itself exhibits that `var a` is hoisted to the top of `foo(..)` (not, obviously, to the top of the program). So the program can perhaps be more accurately interpreted like this:

```jsx
function foo() {
	var a;

	console.log( a ); // undefined

	a = 2;
}

foo();

```

Function declarations are hoisted, as we just saw. But function expressions are not.

```jsx
foo(); // not ReferenceError, but TypeError!

var foo = function bar() {
	// ...
};

```

The variable identifier `foo` is hoisted and attached to the enclosing scope (global) of this program, so `foo()` doesn't fail as a `ReferenceError`. But `foo` has no value yet (as it would if it had been a true function declaration instead of expression). So, `foo()` is attempting to invoke the `undefined` value, which is a `TypeError` illegal operation.

Also recall that even though it's a named function expression, the name identifier is not available in the enclosing scope:

```jsx
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
	// ...
};

```

This snippet is more accurately interpreted (with hoisting) as:

```jsx
var foo;

foo(); // TypeError
bar(); // ReferenceError

foo = function() {
	var bar = ...self...
	// ...
}

```

## Functions First

함수와 변수 둘다 hoist되지만 함수가 더 먼저 우선선위 가진다.

Consider:

```jsx
foo(); // 1

var foo;

function foo() {
	console.log( 1 );
}

foo = function() {
	console.log( 2 );
};

```

`1` is printed instead of `2`! This snippet is interpreted by the *Engine* as:

```jsx
function foo() {
	console.log( 1 );
}

foo(); // 1

foo = function() {
	console.log( 2 );
};

```

Notice that `var foo` was the duplicate (and thus ignored) declaration, even though it came before the `function foo()...` declaration, because function declarations are hoisted before normal variables.

While multiple/duplicate `var` declarations are effectively ignored, subsequent function declarations *do* override previous ones.

```jsx
foo(); // 3

function foo() {
	console.log( 1 );
}

var foo = function() {
	console.log( 2 );
};

function foo() {
	console.log( 3 );
}

```

같은 스코프에 함수명 중복 두개인건 매우 안좋음.

위의 경우 밑에 함수가 적용.

Function declarations that appear inside of normal blocks typically hoist to the enclosing scope, rather than being conditional as this code implies:

```jsx
foo(); // "b"

var a = true;
if (a) {
   function foo() { console.log( "a" ); }
}
else {
   function foo() { console.log( "b" ); }
}

```

블럭 안에서 함수를 선언하는건 피해야한다.
