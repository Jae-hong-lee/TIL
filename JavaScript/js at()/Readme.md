# Array.prototype.at()

at() 메서드는 ES2021(ES12라고도 함)에 도입된 비교적 새로운 JavaScript 추가 기능이다.
`String` 및 `Array` 클래스의 인스턴스에서 사용할 수 있는 메서드이며 간결하고 읽기 쉬운 방식으로 지정된 인덱스에서 요소를 검색할 수 있다.
쉽게 말해, 배열의 특정 인덱스에 위치한 요소를 반환하는 새로운 메서드!

이 메서드는 특히 **음수 인덱스**를 지원하여 배열의 끝에서부터 요소를 쉽게 참조할 수 있도록 한다.
그래서, 배열 요소에 접근하는 더 간결하고 직관적인 방법을 제공할 수 있다.

### 기본문법

```js
// arr 는 배열
const arr = [5, 0];
arr.at(index);

// at을 활용해 출력해보기
const array1 = [5, 12, 8, 130, 44];

let index = 2;
console.log(`An index of ${index} returns ${array1.at(index)}`);
// Expected output: "An index of 2 returns 8"

// 음수일 경우 출력값 변화
index = -2;
console.log(`An index of ${index} returns ${array1.at(index)}`);
// Expected output: "An index of -2 returns 130"
```

index: 배열에서 접근하고자 하는 요소의 인덱스, 음수일 경우 배열의 끝에서부터의 인덱스를 의미한다!

### 주요특징

1. 음수 인덱스 지원: 음수 인덱스를 사용하면 배열의 끝에서 부터 요소에 접근할 수 있다.
2. 배열 경계 검사: `index`가 배열의 범위를 벗어나면 `undefined`를 반환
3. 읽기 전용: `at()` 메서드는 배열을 변경하지 않고, 읽기 전용으로 요소를 반환한다.

배열의 마지막 요소를 참조할 때 array[array.length - 1] 같은 복잡한 표현식을 쓰지 않아도 되기 때문에 가독성이 높아지고, 음수 인덱스를 통해 배열의 마지막 요소들을 손쉽게 참조할 수 있다.

**주의할 점은 최신 브라우저에만 적용된다는 점이다.**

### 활용

주어진 배열의 끝 값(요소)을 반환해주는 함수 만들기

```js
const arrayLast = (array) => array.at(-1);

/* 위에서 만들어준 arrayLast 함수를 이용 */
const arr = ["banana", "orange", "lemon"];
console.log(arrayLast(arr)); // lemon

/* arr에 요소를 추가 후 arrayLast 함수를 이용 */
arr.push("apple");
console.log(arrayLast(arr)); // apple
```

---

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/at)
