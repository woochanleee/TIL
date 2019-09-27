# 실행 컨텍스트
## 자바스크립트 엔진의 실행 컨텍스트 관리 방법
## 🍎간단한 설명
> ### 정의  
실행 컨텍스트란 자바스크립트 코드가 __실행되고 연산되는 범위__ 를 나타내는 추상적 개념이다.
> ### 특징
자바스크립트 엔진은 코드를 실행하기 위해서 실행에 필요한 여러가지 정보를 알고 있어야 한다. 실행에 필요한 여러가지 정보란 아래와 같다.

-   변수 : 전역변수, 지역변수, 매개변수, 객체의 프로퍼티
-   함수 선언
-   변수의 유효범위
-   this

이와 같이 실행에 필요한 정보를 형상화하고 구분하기 위해서 자바스크립트 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리한다.

## 📎 Execution Context

실행 컨텍스트는 쉽게 말해 코드가 실행되고 있는 구역, 범위에 대한 개념이다.

### Types of Exection Context

-   **Global Execution Context -**  가장 기초가 되는 실행 구역이다. 특정 `함수` 안에서 실행되는 코드가 아니라면 코드는 전역 컨텍스트에서 실행된다. 전역 컨텍스트에서는 두 가지 일이 이루어지는데 1)  `window`  오브젝트인 전역 컨텍스트를 생성하고 2)  `this`  를 global object로 할당한다.
-   **Functional Execution Context -**  함수가 호출될 때마다, 해당 함수에 대한 실행 컨텍스트가 생성된다. 각각의 함수들은 자신만의 실행 컨텍스트를 가지지만 실행 컨텍스트는  **함수가 호출이 되어야 만들어진다**.
-   **Eval Function Execution Context -**  `eval`  함수 또한 자신 만의 실행 컨텍스트를 가진다.

## 📎 Execution Stack (호출 스택)과 함수 실행 순서

자바스크립트 엔진이  `script`  tag를 처음 만나면 전역 컨텍스트를 만들고 현재 실행되고 있는 호출 스택에 이를 push 한다. 다른 함수가 호출 되면 해당 함수에 대한 실행 컨텍스트를 생성하고 스택의 제일 꼭대기에 push 한다.

자바스크립트 엔진은 실행 컨텍스트가 호출 스택에서 가장 위에 있는 함수를 실행한다. 함수가 할 일을 마치면 스택에서 제거된다 (pop).

## 자바스크립트 엔진의 실행 컨텍스트 생성 과정
## Execution Context in Detail

실행 컨텍스트는 두 가지 단계로 생성된다.  **1) Creation Phase**  와  **2) Execution Phase**  이다 .

### (1) Creation Phase

Creation phase 동안 두 가지 일이 일어난다.

1.  **LexicalEnvironment**  컴포넌트 생성
2.  **VariableEnvironment**  컴포넌트 생성  
    실행 컨텍스트는 개념적으로 아래와 같다.
    
    ```js
    ExecutionContext = {
    LexicalEnvironment = <ref. to LexicalEnvironment in memory>,
    VariableEnvironment = <ref . to VariableEnvironment in memory>,
    }
    ```
    
    #### Lexical Environment
    
    Lexical Environment는 identifier-variable mapping 되는 곳이다.  
    참조 대상 식별자인 identifier는 함수와 변수의 이름과 같이 어떤 대상을 다른 대상과 구분하여 식별할 수 있는 유일한 이름이다. 자바스크립트는 이 규칙대로 식별자를 찾는다.  **즉 Lexical Environment에서는 변수와 해당 변수에 대입된 값이 매핑되는 곳이라고 할 수 있다. 매핑만 됨 !**

```js
var a = 20;
var b = 40;

function foo(){
    console.log('bar');
}
```

위의 코드에 대한 lexical environment는 아래와 같다.

```js
lexicalEnvironment = {
  a : 20,
  b : 40,
  foo : <ref. to foo function>
}
```

렉시컬 환경은 세가지 일을 한다. (VariableEnvironment도 동일)

1.  Environment Records
2.  Reference to the outer environment
3.  This binding

**1. Environmnet Records**  
lexical environment 안에 함수와 변수를 기록한다.

-   **Declarative environment record --**  변수와 함수 선언을 저장하는 곳이다.
-   **Object environment record --**  전역 코드에 대한 lexical environment는 objective environment records를 포함한다. 변수와 함수의 선언과 다르게 object environment record는 글로벌 오브젝트도 기록한다. 각각의 객체의 속성을 바인딩하기 위해서 record에 새로운 엔트리가 형성된다.

**2. Reference to the outer environment**  
외부 환경으로의 참조 값의 의미는 외부 lexical 환경으로 접근할 수 있다는 의미이다. 자바스크립트 엔진이 현재의 lexical environment에서 변수를 찾지 못했다면  **외부 환경에서 해당 변수를 찾아 볼 수 있다**는 의미이다.

**3. This binding**  
`this`의 값이 여기서 결정된다. 글로벌 실행 컨텍스트에서  `this`는 global object 이다.  
함수 실행 컨텍스트에서는  `this`  값은 어떻게 함수가 호출되었는지에 따라 달라진다. 만약 함수가 object reference로 호출되었다면  `this`는 해당 객체를 가리키게 된다. 그렇지 않으면 this는 글로벌 객체(window)를 가리키거나 strict mode에서는  `undefined`를 가리키고 있다.

> **this**와 함수 호출
> 
> ```js
> const person = {
> name : 'peter',
> birthYear : 1994,
>   calcAge: function(){
>     console.log(2018- this.birthYear);
>     }
> }
> person.calcAge();
> const calcuateAge = person.calcAge;
> calculateAge();
> ```
> 
> -   `person.clacAge()`  calcAge는 'person' object reference로 호출되었기 때문에 여기서 "this"는 'person'을 가리키게 된다.
> -   `calculateAge()`  여기서는 주어진 객체 참조값이 없기 때문에 this는 글로벌  `window`  객체를 가리키게 된다.


#### VariableEnvironment

LexicalEnvironment 와 funtion, 변수 식별자가 binding 되는 점을 포함해 동일하다. VariableEnvironment 또한 Lexical Environment이다. ES6 에서 LexicalEnvironment와 VariableEnvironment 둘의 차이점은 전자가 함수 선언과 변수 (`let`과  `const`)의 바인딩을 저장하고 후자는 변수  `var`  만 저장한다.

> ✅ 정리해보면 실행 컨텍스트 Creation Phase 에서는
> 
> -   Scope Chain, 변수, 함수, 인자들을 만든다.
> -   `this`  를 결정한다.
> -   자바스크립트 엔진의 Syntax Parser가 코드를 읽으면서 컴퓨터가 알아들을 수 있는 언어로 변환된다.
> -   자바스크립트 엔진은 코드를 읽으면서 변수와 함수의 선언된 것을 찾고 메모리에 해당 변수와 함수를 저장한다. (호이스팅)

### (2) Execution Phase

```js
let a = 20;
const b = 30;
var c;

function multiply(e, f){
 var g = 20;
  return e * f * g;
}
c= multiply(20,30);
```

1.  자바스크립트 엔진은 script를 만나면 전역 컨텍스트를 만든다. 전역 변수 a,b, multiply는 LexicalEnvironment에 c는 VariableEnvironment에서  `이름: 값`  매핑이 된다.  
    ![스크린샷 2019-03-09 오후 11.50.23.png](https://images.velog.io/post-images/imacoolgirlyo/c4abfd80-427a-11e9-b77a-574f9b588be3/-2019-03-09-11.50.23.png)
    
2.  전역 컨텍스트의 Execution Phase에서 코드가 실행되고 각각의 변수에 값이 할당된다.  
    ![스크린샷 2019-03-09 오후 11.50.32.png](https://images.velog.io/post-images/imacoolgirlyo/c7e15ef0-427a-11e9-b77a-574f9b588be3/-2019-03-09-11.50.32.png)
    
3.  multiply() 함수가 호출되는 순간 multiply() 함수의 실행컨텍스트가 만들어진다. 외부 환경으로는 전역 컨텍스트를 참조하고 있고, object reference로 호출되지 않았기 때문에 여전히 this는 글로벌 오브젝트이다.  
    ![스크린샷 2019-03-09 오후 11.50.40.png](https://images.velog.io/post-images/imacoolgirlyo/cb499030-427a-11e9-b77a-574f9b588be3/-2019-03-09-11.50.40.png)
    
4.  multiply 함수가 값을 리턴하면서 호출 스택에서 빠진다. 전역 컨텍스트에 있던 전역 변수 c에 값이 업데이트 된다. 그 후 글로벌 코드가 끝나면 프로그램도 종료된다.  
    ![스크린샷 2019-03-09 오후 11.50.48.png](https://images.velog.io/post-images/imacoolgirlyo/ce2b2c50-427a-11e9-b77a-574f9b588be3/-2019-03-09-11.50.48.png)
    

**Note**  
`let`과  `const`는 실행 컨텍스트가 만들어질 때 (creation phase) 어떤 값도 가지고 있지 않다. 하지만  `var`는  `undefined`를 가지고 있다.

그 이유는 실행 컨텍스트가 만들어지는 동안, 코드는 변수와 함수 선언을 위해 스캔된다. 이때 함수의 선언은 environment에 함수 전체가 다 저장되지만, 변수들은 기본 값으로  `undefined`나 아직 초기화 되지 않은 상태로 저장된다. (호이스팅 개념)

이 때문에  `var`  변수가 선언되기 전에  `undefined`라는 값으로 접근 할 수 있는 것이고  `let`과  `const`를 선언하기 전에 접근하면 reference error를 얻게 되는 것이다.

이것을 호이스팅이라고 부른다.

execution phase 동안 자바스크립트 엔진이 소스 코드에서  `let`  변수의 값이 선언된 곳을 찾지 못하면  `undefined`를 할당한다.

> 🎈 https://velog.io/@imacoolgirlyo/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-Hoisting-The-Execution-Context-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-6bjsmmlmgy 의 내용을 읽고 정리하였습니다.
