# TypeScript Pick

### 타입스크립 유틸리티 타입

유틸리티 타입이란? 타입스크립트가 자체적으로 제공하는 특수한 타입들, 코드작성과 유지보수를 더 쉽게 할 수 있도록 도와준다.
유틸리티 타입은 이미 정의되어 있는 타입 구조를 변경하여 재사용하고 싶을때 주로 사용된다.

#### Pick<T,K>

Pick 은 T의 특정 프로퍼티(K) 들만 선택하여 새로운 타입을 만든다.

```ts
interface User {
  id: number;
  name: string;
  age: number;
  email: string;
}

type UserIdAndName = Pick<User, "id" | "name">; // id와 name 프로퍼티만 선택(여러개)
type UserAge = Pick<User, "age">; // 한개만도 가능
```

```ts
interface Product {
  id: number;
  name: string;
  price: number;
  brand: string;
  stock: number;
}

// 상품 목록을 받아오기 위한 api
function fetchProduct(): Promise<Product[]> {
  // ... id, name, price, brand, stock 모두를 써야함
}

type shoppingItem = Pick<Product, "id" | "name" | "price">;

// 상품의 상세정보 (Product의 일부 속성만 가져온다)
function displayProductDetail(shoppingItem: shoppingItem) {
  // id, name, price의 일부만 사용 or 별도의 속성이 추가되는 경우가 있음
  // 인터페이스의 모양이 달라질 수 있음
}
```
