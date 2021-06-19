# You Don't Know JS: Scope & Closures
# Chapter 1: Scope란?

프로그래밍에서 주로 값을 변수안에 넣고 나중에 값을 가져오거나 수정하는데 사용.

이러한 능력이 *상태*.

어디에 변수가 있나?

변수를 찾아야하는 공간이 *Scope*

## 컴파일러 이론

JS는 "dynamic" or "interpreted" 언어, 사실은 compiled language ?


JavaScript 엔진은 여러 단계를 거친다.

전통적인 컴파일 언어는 아래의 단계를 따른다. "compilation"라고 부르는:

1. **Tokenizing/Lexing:** 스트링을 의미있는 청크로 나누는 (토큰). 예를들어 `var a = 2;`는 다음과 같이 토큰으로 쪼개짐: `var`, `a`, `=`, `2`, and `;`. 공백은 의미있는지 없는지에 따라 포함되기도하고 아니기도.

    **Note:** 토큰나이징과 렉싱의 차이는 미묘하고 아카데믹, 중점은 *stateless* or *stateful*. `a` should be considered a distinct token or just part of another token, *that* would be **lexing**.

2. **파싱:** 토큰의 스트림 (배열)을 중첩된 엘레먼트 트리로 표현, 종합적으로 프로그램의 문법을 보여주는. 이 트리는 "AST"라고 불린다 (<b>A</b>bstract <b>S</b>yntax <b>T</b>ree).

    `var a = 2;`의 트리는 `VariableDeclaration`라는 Top 노드로 시작해서, 자식노드 `Identifier` (`a` 변수), 그리고 다른 자식 `AssignmentExpression` `NumericLiteral`라는 2의 값을 가진.

3. **코드 생성:** AST를 받아서 실행가능한 코드로 바꾸는 과정. 언어나 플랫폼에 따라 다름.

자바스크립트 엔진은 위의 3가지 단계보다 훨씬 복잡하다.

우선은 컴파일이 되고 실행된다라고..

## Scope 이해하기

대화하는 단계를 생각하면 된다 (?)

### 등장인물

1. *엔진*: 시작에서 끝 컴파일과 자바스크립트 실행을 담당

2. *컴파일러*: *엔진*'의 친구 중 한명; 파싱과 코드생성을 담당

3. *스코프*: *엔진*의 다른 친구; 모든 선언된 identifiers (변수)를 수집하고 관리, 현재 코드에서 접근 가능한지 엄격한 규칙 강요.

### Back & Forth

`var a = 2;`를 보면 하나의 문장(?)같지만 *엔진*이 보는 시각은 다르다.

*엔진* 은 두개의 다른 문장(?)으로 본다. *컴파일러*가 컴파일 중 핸들 하는 거 1개, *엔진*이 실행 중 핸들하는 1개.

*컴파일러*가 먼저하는건 렉싱하여 토큰으로 쪼개고 트리로 파싱.

그러나 *컴파일러*가 코드 생성 할때는 가정한것과는 다르게 작동한다.

*컴파일러*가 할 일을 합리적으로 가정해보면: "`a`라는 변수의 메모리를 할당하고, `2`라는 값을 변수에 지정." 하지만 이렇게 하지 않는다.

*컴파일러*는 실제로:

1. `var a`를 보고 *스코프*에 이미 존재하는 변수인지 물어본다. 존재한다면 *Compiler*는 무시하고 넘어간다. 존재하지않으면 *Compiler*는 *Scope*에 `a`라는 걸 스코프 컬렉션에 넣어라고 한다.

2. *Compiler*는 *Engine*이 실행할 코드를 만든다. *Scope*에 `a`가 있고 현재 스코프 컬렉션에서 접근 가능한지 물어본다. 찾으면 *Engine*은 그 변수를 사용한다. 못찾으면 *Engine* 은 *다른곳*에서 찾는다.

*Engine*이 끝내 변수를 찾으면, 값 `2`를 지정한다. 못찾으면 *Engine*은 손을 들고 에러를 외친다 ㅡ.ㅡ

결론: 변수 지정에 2가지의 다른 액션이 있다: 1번, *Compiler*가 변수를 선언, 2번째, 실행 중에, *Engine*이 *Scope*에서 찾고 지정한다.

### Compiler Speak

어떤것을 lookup 하는지에 따라 결과값이 다르다.

LHS와 RHS가 있다. 각각 "Left-hand Side", "Right-hand Side".

**assignment 연산**의 사이드

```js
console.log( a );
```

`a` 레퍼런스는 RHS 레퍼런스이다. `a`는 여기에서 아무 값이 지정되지 않았기 때문. `a`의 값을 찾을 것, `console.log(..)`에 넘기기 위해.

반대로:

```js
a = 2;
```

`a`의 레퍼런스는 LHS이다. 현재 값이 뭔지 상관없고 `= 2`를 타켓으로하는 변수를 찾으면 된다.


LHS and RHS references 다 가지고 있는 예:


```js
function foo(a) {
	console.log( a ); // 2
}

foo( 2 );
```

The last line that invokes `foo(..)` as a function call requires an RHS reference to `foo`, meaning, "go look-up the value of `foo`, and give it to me." Moreover, `(..)` means the value of `foo` should be executed, so it'd better actually be a function!

There's a subtle but important assignment here. **Did you spot it?**

You may have missed the implied `a = 2` in this code snippet. It happens when the value `2` is passed as an argument to the `foo(..)` function, in which case the `2` value is **assigned** to the parameter `a`. To (implicitly) assign to parameter `a`, an LHS look-up is performed.

There's also an RHS reference for the value of `a`, and that resulting value is passed to `console.log(..)`. `console.log(..)` needs a reference to execute. It's an RHS look-up for the `console` object, then a property-resolution occurs to see if it has a method called `log`.

Finally, we can conceptualize that there's an LHS/RHS exchange of passing the value `2` (by way of variable `a`'s RHS look-up) into `log(..)`. Inside of the native implementation of `log(..)`, we can assume it has parameters, the first of which (perhaps called `arg1`) has an LHS reference look-up, before assigning `2` to it.

**Note:** You might be tempted to conceptualize the function declaration `function foo(a) {...` as a normal variable declaration and assignment, such as `var foo` and `foo = function(a){...`. In so doing, it would be tempting to think of this function declaration as involving an LHS look-up.

However, the subtle but important difference is that *Compiler* handles both the declaration and the value definition during code-generation, such that when *Engine* is executing code, there's no processing necessary to "assign" a function value to `foo`. Thus, it's not really appropriate to think of a function declaration as an LHS look-up assignment in the way we're discussing them here.

### Engine/Scope 대화

```js
function foo(a) {
	console.log( a ); // 2
}

foo( 2 );
```

> ***Engine***: Hey *Scope*, I have an RHS reference for `foo`. Ever heard of it?

> ***Scope***: Why yes, I have. *Compiler* declared it just a second ago. He's a function. Here you go.

> ***Engine***: Great, thanks! OK, I'm executing `foo`.

> ***Engine***: Hey, *Scope*, I've got an LHS reference for `a`, ever heard of it?

> ***Scope***: Why yes, I have. *Compiler* declared it as a formal parameter to `foo` just recently. Here you go.

> ***Engine***: Helpful as always, *Scope*. Thanks again. Now, time to assign `2` to `a`.

> ***Engine***: Hey, *Scope*, sorry to bother you again. I need an RHS look-up for `console`. Ever heard of it?

> ***Scope***: No problem, *Engine*, this is what I do all day. Yes, I've got `console`. He's built-in. Here ya go.

> ***Engine***: Perfect. Looking up `log(..)`. OK, great, it's a function.

> ***Engine***: Yo, *Scope*. Can you help me out with an RHS reference to `a`. I think I remember it, but just want to double-check.

> ***Scope***: You're right, *Engine*. Same guy, hasn't changed. Here ya go.

> ***Engine***: Cool. Passing the value of `a`, which is `2`, into `log(..)`.

> ...

### Quiz

Check your understanding so far. Make sure to play the part of *Engine* and have a "conversation" with the *Scope*:

```js
function foo(a) {
	var b = a;
	return a + b;
}

var c = foo( 2 );
```

1. Identify all the LHS look-ups (there are 3!).

LMS lookup for a, LMS lookup for b, LMS lookup for c

2. Identify all the RHS look-ups (there are 4!).

foo(),

## 중첩된 스코프

실제로는 중첩된 블럭, 함수가 많이 존재한다.

immediate scope에서 찾지못하면 *Engine*은 다음 포함된 밖의 스코프에 물어본다.

Consider:

```js
function foo(a) {
	console.log( a + b );
}

var b = 2;

foo( 2 ); // 4
```

The RHS reference for `b` cannot be resolved inside the function `foo`, but it can be resolved in the *Scope* surrounding it (in this case, the global).

So, revisiting the conversations between *Engine* and *Scope*, we'd overhear:

> ***Engine***: "Hey, *Scope* of `foo`, ever heard of `b`? Got an RHS reference for it."

> ***Scope***: "Nope, never heard of it. Go fish."

> ***Engine***: "Hey, *Scope* outside of `foo`, oh you're the global *Scope*, ok cool. Ever heard of `b`? Got an RHS reference for it."

> ***Scope***: "Yep, sure have. Here ya go."

The simple rules for traversing nested *Scope*: *Engine* starts at the currently executing *Scope*, looks for the variable there, then if not found, keeps going up one level, and so on. If the outermost global scope is reached, the search stops, whether it finds the variable or not.

## Errors

LHS와 RHS가 변수 못찾았을 때 행동하는게 다르다.

Consider:

```js
function foo(a) {
	console.log( a + b );
	b = a;
}

foo( 2 );
```

RHS look-up `b`을 처음 시도하면 못찾는다. This is said to be an "undeclared" variable, because it is not found in the scope.

If an RHS look-up fails to ever find a variable, anywhere in the nested *Scope*s, this results in a `ReferenceError` being thrown by the *Engine*. It's important to note that the error is of the type `ReferenceError`.

By contrast, if the *Engine* is performing an LHS look-up and arrives at the top floor (global *Scope*) without finding it, and if the program is not running in "Strict Mode" [^note-strictmode], then the global *Scope* will create a new variable of that name **in the global scope**, and hand it back to *Engine*.

*"No, there wasn't one before, but I was helpful and created one for you."*

"Strict Mode" [^note-strictmode], which was added in ES5, has a number of different behaviors from normal/relaxed/lazy mode. One such behavior is that it disallows the automatic/implicit global variable creation. In that case, there would be no global *Scope*'d variable to hand back from an LHS look-up, and *Engine* would throw a `ReferenceError` similarly to the RHS case.

Now, if a variable is found for an RHS look-up, but you try to do something with its value that is impossible, such as trying to execute-as-function a non-function value, or reference a property on a `null` or `undefined` value, then *Engine* throws a different kind of error, called a `TypeError`.

`ReferenceError` is *Scope* resolution-failure related, whereas `TypeError` implies that *Scope* resolution was successful, but that there was an illegal/impossible action attempted against the result.

## Review (TL;DR)

Scope is the set of rules that determines where and how a variable (identifier) can be looked-up. This look-up may be for the purposes of assigning to the variable, which is an LHS (left-hand-side) reference, or it may be for the purposes of retrieving its value, which is an RHS (right-hand-side) reference.

LHS references result from assignment operations. *Scope*-related assignments can occur either with the `=` operator or by passing arguments to (assign to) function parameters.

The JavaScript *Engine* first compiles code before it executes, and in so doing, it splits up statements like `var a = 2;` into two separate steps:

1. First, `var a` to declare it in that *Scope*. This is performed at the beginning, before code execution.

2. Later, `a = 2` to look up the variable (LHS reference) and assign to it if found.

Both LHS and RHS reference look-ups start at the currently executing *Scope*, and if need be (that is, they don't find what they're looking for there), they work their way up the nested *Scope*, one scope (floor) at a time, looking for the identifier, until they get to the global (top floor) and stop, and either find it, or don't.

Unfulfilled RHS references result in `ReferenceError`s being thrown. Unfulfilled LHS references result in an automatic, implicitly-created global of that name (if not in "Strict Mode" [^note-strictmode]), or a `ReferenceError` (if in "Strict Mode" [^note-strictmode]).

### Quiz Answers

```js
function foo(a) {
	var b = a;
	return a + b;
}

var c = foo( 2 );
```

1. Identify all the LHS look-ups (there are 3!).

	**`c = ..`, `a = 2` (implicit param assignment) and `b = ..`**

2. Identify all the RHS look-ups (there are 4!).

    **`foo(2..`, `= a;`, `a + ..` and `.. + b`**
