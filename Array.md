# Array.prototype.map()
## 설명
> ### 정의
map() 메서드는 배열 내의 __모든 요소마다__ 주어진 함수를 실행한 결과를 모아 __새로운 배열을 반환__ 한다.

> ### 특징
`callback` 함수는 호출될 때 대상 **요소의 값**, 그 **요소의 인덱스**, 그리고 `map`을 호출한 **원본 배열**  **3개의 인수**를 전달받습니다.
``` javascript
let array = ["first", "second", "third"];
array.map(function(currentValue, index, array){
	console.log(currentValue, index, array);
});
// first 0 (3) ["first", "second", "third"]
// second 1 (3) ["first", "second", "third"]
// third 2 (3) ["first", "second", "third"]
```
`calback` 함수에서 리턴값을 안주면 **undifined**로 배열에 값이 들어간다.
``` js
let array = [1, 2, 3];
let map1 = array.map(() => {console.log(1);});
console.log(map1);
// [undefined, undefined, undefined]
```
 `map`은 호출한 배열의 값을 변형하지 않는다. 단, `callback` 함수에 의해서 변형될 수 있다.
 ``` js
var array = [1, 4, 9, 16];
const map = array.map(x => x * 2);
console.log(map);
// [2, 8, 18, 32]

 ```

## 구문
```
arr.map(callback(currentValue[, index[, array]])[, thisArg])
```

> ### 매개변수

`callback`

각 요소마다 실행할 함수. 다음 세 가지 인수를 가진다.

`currentValue`

처리할 현재 요소.

`index` Optional

처리할 현재 요소의 인덱스.

`array` Optional

- `map()`을 호출한 배열.

`thisArg` Optional

- `callback`을 실행할 때  `this`로 사용되는 값.

> ### 반환 값

배열의 각 요소에 대해 실행한  `callback`의 결과를 모은 새로운 배열.

# Array.prototype.forEach()

## 설명

> ### 정의

forEach() 메서드는 주어진 함수를 배열 요소 각각에 대해 실행한다.



> ### 특징



``` js
let array = [1, 2, 3];

array.forEach((value) => {console.log(value);});
```



## 구문

```javascript
arr.forEach(callback[, thisArg]);
```

### 매개변수

- `callback`

  각 요소에 대해 실행할 함수. 다음 세 가지 인수를 받는다.

  `currentValue`처리할 현재 요소.

  `index` Optional

  처리할 현재 요소의 인덱스.

  `array` Optional

  forEach()를 호출한 배열.

- `thisArg` Optional

  `callback`을 실행할 때 `this`로 사용할 값.

### 반환 값

[`undefined`]

## 궁금증

map() 과 forEach() 는 동작 방법이 매우 유사해보인다. 무엇이 비슷하고 다를까?

1. 둘다 배열을 돌면서 인자로 전달한 값으로 로직을 구현하는것은 비슷하다.
2. 하지만 return이 있는지 없는지에 차이가 있다.
3. ㅁㄴㅇㄹ

모질라 개발자 네트워크(Mozilla Developer Network, 이하 MDN)에서는 [forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 는 return value 가 undefined 인 반면, [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 은 콜백 함수의 결과 값들로 구성된 새로운 배열(A new array with each element being the result of the callback function)을 return 한다고 이야기한다.

따라서 어떤 배열이 있을 때 배열을 순회하며 원소의 값들을 각각 가공해서 수정된/새로운 배열(원래 배열과 길이가 똑같은)을 return 받고자 한다면 map 메서드를, 그렇지 않고 원소의 값들을 활용해서 원소들의 합이나, 평균을 구하고자 한다면, 그리고 원래 배열과는 길이가 다른 배열 결과를 받고 싶다면, forEach 를 쓰면 될 것 같다.

``` js
let data= [1, 2, 3, 4, 5]
// forEach 를 사용해서 원소의 값들을 각각 3씩 증가시키기
let result = [];
data.forEach(x => { result.push(x + 3) });
console.log(result);
// map 을 사용해서 원소의 값들을 각각 3씩 증가시키기
let result2 = data.map(x => x + 3);
console.log(result2);
```

