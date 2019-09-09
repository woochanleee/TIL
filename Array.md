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
