# JS 에 new Map 과 Object

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/724015be-c516-4430-90b8-00cd5e0f9d10)

JS 의 `Map` 과 객체(`Object`)는 `Key-Value` 쌍으로 동작한다는 점에서는 비슷하다.
그럼 무슨 차이를 가지고 있는걸까? 인용문을 통해 짐작해보자.

> 물건 창고를 상상해보세요. 그리고 그 창고 안에는 다양한 물건들이 쌓여있다고 상상해보죠.
>
> 이때 map과 object는 두 가지 다른 방식으로 물건을 분류하고 관리하는 방법을 나타냅니다.
>
> map은 창고의 관리자가 주어진 물건에 대해 레이블을 붙이는 방식으로 동작합니다.
> 예를 들어, 빨간색 상자 안에는 사과, 파란색 상자 안에는 파란색 연필 노란색 상자 안에는 노란색 휴대폰과 같이 특정 키에 연결된 값을 저장합니다. 이렇게 키-값 쌍을 이용하여 데이터를 저장하고 조회할 수 있습니다. 키를 통해 해당하는 값을 찾아내거나 업데이트할 수 있어요. 또한, map은 데이터의 순서가 보장되지 않으며, 동일한 키를 가진 항목은 허용되지 않습니다.
>
> 반면 object는 창고 안의 물건들을 이름표를 붙이는 방식으로 관리합니다. 물건마다 고유한 이름표(프로퍼티)를 부여하고 그에 해당하는 값을 저장합니다. 예를 들어, "apple"이라는 이름표에는 사과, "bluePencil"이라는 이름표에는 파란색 연필과 같이 프로퍼티와 그에 연결된 값을 가지고 있습니다. 이렇게 이름표를 사용하여 데이터에 접근하고 업데이트할 수 있습니다. 객체는 프로퍼티를 통해 값을 가져오거나 변경할 수 있고, 객체의 프로퍼티 개수를 확인할 수 있습니다.
>
> 창고를 관리하면서 map을 사용하면 키-값 쌍으로 물건을 구분하고 조회하며, object를 사용하면 이름표로 물건을 식별하고 접근합니다. 이러한 방식을 통해 map과 object는 데이터를 구조화하고 관리하는 데 유용한 도구가 됩니다.
>
> 출처: https://2minmin2.tistory.com/44 [민민의 하드디스크:티스토리]

### 1. 키의 타입

**Object**: 키는 문자열(String) or 심볼(Symbol) 타입만 가능하다. 숫자나 다른 타입의 키는 자동으로 문자열로 변환된다.

```js
let obj = {};
obj["1"] = "one"; // 문자열 키
obj[1] = "one"; // 숫자 키가 자동으로 문자열 '1'로 변환됨
console.log(obj); // { '1': 'one' }
```

**Map**: 키로 모든 값을 사용할 할 수 있다. (원시, 객체 모두가능)

```js
let map = new Map();
map.set(1, "one"); // 숫자 키
map.set("1", "one"); // 문자열 키
console.log(map); // Map(2) { 1 => 'one', '1' => 'one' }
```

### 2. 키의 순서

**Object**: 키의 순서는 삽입 순서와 다를 수 있다. 일반적으로 문자열 키는 삽입순서를 유지, 숫자 키는 자동정렬된다.
**Map**: 키의 삽입 순서 유지

```js
let map = new Map();
map.set("a", 1);
map.set("b", 2);
console.log(map.keys()); // MapIterator { 'a', 'b' }
```

### 3. 성능

**Object**: 일반적인 키-값 저장에 성능이 최적화되어 있다.
**Map**: 대량의 요소를 저장하거나 빈번한 삽입, 삭제 작업에서 성능이 더 나을 수 있다. 특히 객체 키를 사용할 때는 Map이 더 효율적이라고 한다.

### 4. 프로토타입 상속

**Object**: 기본적으로 프로토타입을 상속받아 생성, 따라서 상속된 프로퍼티나 메서드가 있을 수 있다.

```js
let obj = {};
console.log(obj.toString); // [Function: toString] (Object의 프로토타입 메서드)
```

**Map**: 프로토타입 상속이 없다. 순수한 키-값 저장소

```js
let map = new Map();
console.log(map.toString); // [Function: toString] (Map의 자체 메서드)
```

### 5. 반복

**Object**: `for...in`문이나 `Object.keys()`, `Object.values()`, `Object.entries()` 등을 사용하여 반복할 수 있다.

```js
let obj = { a: 1, b: 2 };
for (let key in obj) {
  console.log(key, obj[key]);
}
```

**Map**: `for...of`문을 사용하거나 `map.keys()`, `map.values()`, `map.entries()` 메서드로 반복할 수 있다.

```js
let map = new Map([
  ["a", 1],
  ["b", 2],
]);
for (let [key, value] of map) {
  console.log(key, value);
}
```

### 6. 크기 확인

**Object**: 크기를 직접 확인하는 방법이 없다(?).
`Object.keys(obj).length`를 사용해야 한다.

> `.length` 는 Object 객체에 직접 내장된 속성이 아니고, 객체의 키를 배열로 변환한 다음 그 배열의 길이를 구하는 방식.

```js
let obj = { a: 1, b: 2 };
console.log(Object.keys(obj).length); // 2
```

**Map**: size 속성을 사용하여 직접 크기를 확인할 수 있다.

> `.size`는 Map 속성을 통해 직접 크기를 확인할 수 있다.

```js
let map = new Map([
  ["a", 1],
  ["b", 2],
]);
console.log(map.size); // 2
```

##### 그래서 뭘 써?

- Object는 문자열 또는 심볼 키-값 쌍을 저장하는 데 적합하며, 기본적인 데이터 구조.
- Map은 모든 타입의 키를 저장할 수 있고, 삽입 순서를 유지하며, 대량의 데이터 처리에 효율적.

둘 중 어떤 것을 사용할지는 특정 용도와 데이터 구조에 따라 결정하면 된다.
