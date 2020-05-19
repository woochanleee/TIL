# Function
## 설명
> ### 정의
함수는 **작업을 수행**하거나 값을 계산하는 **문장들의 집합**이다.
> ### 특징
- JavaScript에서 모든 함수는  **`Function`** 객체이다.
- 다른 객체와 함수를 구별하는 것은 함수는 **호출** 될 수 있다.
- 함수에 값을 **전달할**수 있고 함수는 값을 **반환**한다.
	- 값을 반환하려면 **`[return] 문이 있어야 한다.`**
	- 함수를 호출할때의 매개변수는 함수의 __인수__ 이다. 인수는 함수에  __값으로__  전달된다.
	- 함수가 인수값을 바꾸면, 이 변화는 호출한 값에 **반영되지 않는다**.
	- 그러나, 객체 참조(reference)는 값이지만 특별하다. 함수가 참조된 객체의 프로퍼티를 바꾸면, 그 변화는 다음 예에서 __함수 밖에서도 바뀌는 것__ 을 볼 수 있다.
```javascript 
/* 함수 'wcFunc' 선언 */
function wcFunc(theValue) {
	theValue= 10;
}
/* 변수 'theValue' 0으로 초기화; */
let theValue= 0;
/* Logs 0 */
console.log(theValue);
/* 'theValue'를 10으로 바꿈 */
wcFunc(value);
/* 함수 내의 'theValue'에 변화는 전역변수 'theValue'에 영향을 주지 않음 */
/* Logs 0 */
console.log(theValue);

/* 함수 'wcFunc2' 선언 */
function wcFunc2(theObject) {
   theObject.height= 180;
 }

 /*
  * 변수 'WOOCHAN' 선언;
  * 새 객체를 만들고 초기화;
  * 'WOOCHAN'에 객체 참조를 할당
  */
 var WOOCHAN = {
   year: 2003,
   name: "LeeWooChan",
   height: 170
 };

 /* Logs 170 */
 console.log(WOOCHAN.height);

 /* 객체 참조를 함수에 전달 */
 wcFunc2(WOOCHAN);

 /*
  * 함수에 의해 바뀌었기에 객체의
  * 'height' 속성의 값으로 0 출력.
  */
 console.log(WOOCHAN.height);
```

## 함수 정의하기

> ### 함수 선언 (`function`  문)
``` js
function name(param) {
   contents
}
```

`name`

함수 이름.

`param`

함수에 전달되는 인수의 이름. 함수는 255개까지 인수를 가질 수 있다.

`contents`

처리할 구문.
> ### 함수 표현식 (`function`  식),  익명함수

``` js
var wcFunction = function() {
    contents
}
```

# Arrow Function

### 설명
> ### 정의

ES6에서 새로 만든 화살표 표기법이다. function이라는 단어와 중괄호 숫자를 줄여 간단하게 표현하려고 고안된 문법이다. 
> ### function문과의 차이
- function을 생략해도 된다.
- 함수에 매개변수가 단 하나 뿐이라면 괄호( ( ) ) 도 생략할 수 있다.
- 함수 바디가 한줄의 구문이라면 중괄호와 return 문을 생략할 수 있고, 암묵적으로 return 된다.
- 화살표 함수는 항상 익명이다. 
- 다음 예제들은 같은 코드이다.
``` js
const a = function() { return "I'm arrow function!"; }
// 또는
const a = () => "I'm arrow function!";

const b = function(name) { return `Hello, ${name}!`; }
// 또는
const b = name => `Hello, ${name}!`;
```
# call, apply, bind
> ### call

call 메서드는 모든 함수에서 사용할 수 있으며, **this를 특정 값으로 지정**할 수 있다.
```js
const woochan= { name : "Woochan" };
const choco = { name : "Choco " };

/* 이 함수는 어떤 객체에도 연결되지 않았지만 this를 사용한다. */
function greet() {
	return `Hello, I'm ${this.name}!`;
}

greet();				// "Hello, I'm undefined!" - this는 어디에도 묶이지 않았다.
greet.call(woochan);	// "Hello, I'm Woochan!" - this는 woochan다.
greet.call(choco);		// "Hello, I'm Choco!" - this는 choco다.
```
**call의 첫 번째 매개변수는 this로 사용할 값**이고, 매개변수가 더 있으면 그 매개변수는 호출하는 함수로 전달된다.
> ### apply

apply는 함수의 매개변수를 처리하는 방법을 제외하면 call과 완전히 같다.
call은 일반적인 함수와 마찬가지로 매개변수를 직접 받지만, **apply는 매개변수를 배열**로 받는다.
apply는 배열 요소를 함수 매개변수로 사용해야 할 때 유용하다.

> ### bind

bind를 사용하면 함수의 **this 값을 영구히 바꿀 수 있다.**

# arrow function 과 일반 function 차이점
> ### 1.this와 arguments의 차이
- 화살표 함수는 `this`와 `arguments`를 바인딩하지 않는다. 그 대신, 일반적인 `this`와 `arguments`와 동일한 범위를 가지고 있다.

### 바인딩이란?
- 변수나 함수사이에서 연관되어있는 관계이다.
``` javascript
function createObj(){
	console.log(`Inside createObj: ${this.name}`);
	return {
		name: "woochan",
		printName(){
			console.log(`Inside printName: ${this.name}`);
		},
	};
}

createObj.call({ name: "I'm upset!!!" }).printName();
// Inside createObj:I'm upset!!!
// Inside printName:woochan
```

``` javascript
function createObj(){
	console.log(`Inside createObj: ${this.name}`);
	return {
		name: "woochan",
		printName: () => console.log(`Inside printName: ${this.name}`),
	};
}

createObj.call({ name: "i'm happy!!!" }).printName();
// Inside createObj: i'm happy!!!
// Inside printName: i'm happy!!!	 
```
### 결론
즉, 화살표 함수안에서의 `this`는 `createObj`안의 `this`를 따르게 된다. 이는 화살표 함수가 현재 환경의 `this`를 따르게 하고 싶을 때 유용하다는 뜻이다. 이 말인 즉슨, 화살표함수에서는 `bind`와 `call`을 사용할 수 없다는 뜻이기도 하다.

> ### 2. 화살표 함수는  `new`로 호출할수 없다.
es2015에서는  `callable`한 것과  `constructable`한 것과의 차이를 두고 있다. 어떤 함수가  `constructable`하다면, 이는  `new`로 호출되어야 한다. ex)  `new Date()`  그리고 만약 함수가  `callable`하다면, 이 함수는  `new`없이도 호출이 되어야 한다 . ex) 일반적인 함수 호출

일반적인 함수의 경우  `callable`하며  `constructable`하다. 그러나 화살표 함수는 오로지  `callable`할 뿐이다. 반대로  `class`의 경우에는 오로지  `constructable`할 뿐이다.

> ### 정리
## 💫정리

### 서로 바꿔서 쓸 수 있는 경우

-   `this`,  `arguments`를 쓰지 않는 경우
-   `bind(this)`를 사용하는 경우

### 서로 바꿔쓸 수 없는 경우

-   `constructable`  함수
-   `prototype`에 추가된 함수나 메소드
-   `arguments`를 함수의 인자로 사용하는 경우
