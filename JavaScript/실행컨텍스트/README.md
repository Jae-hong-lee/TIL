# 실행컨텍스트 (execution context)

## ⭐️ 정의

**코드의 실행환경에 대한 여러가지 정보를 담고 있는 개념** 으로, 간단히 말하자면 자바스크립트 엔진에 의해 만들어지고 사용되는 코드 정보를 담은 객체의 집합이라고 할 수 있다.

> 코어 자바스크립트🔥
> 실행 컨텍스트(execution context)는 실행할 코드에 제공할 환경 정보들을
> 모아놓은 객체로, 자바스크립트의 동적 언어로서의 성격을 가장 잘 파악할 수 있는 개념이다.

## ⭐️ 종류

자바스크립트의 코드는 3가지 종류로 이루어지는데,**글로벌 스코프**에서 실행하는 **글로벌 코드**, **함수 스코프**에서 실행하는 **함수 코드** 그리고 여기서 다루진 않지만 `eval()` 로 실행되는 코드가 있다. 이 각각의 코드는 자신만의 실행 컨텍스트를 생성한다.

> "eval is evil" - Douglas Crockford
> eval 함수는 문자열로 된 자바스크립트 코드를 전달하면 그대로 실행되는 함수인데, 속도나 보안이 좋지 않아 현재는 거의 쓰지 않는다

엔진이 스크립트 파일을 실행하기 전에 **글로벌 실행 컨텍스트(Global Execution Context, GEC)** 가 생성되고,
함수를 호출할 때마다 **함수 실행 컨텍스트(Function Execution Context, FEC)** 가 생성된다.
주의할 점은, 글로벌의 경우 실행 이전에 생성되지만 함수의 경우 호출할 때 생성된다는 점

따라서 자동으로 생성되는 전역공간과 eval을 제외하면, **실행 컨텍스트가 생성되는 시점은 곧 함수를 실행하는 시점**이다.

## ⭐️ 실행 컨텍스트 스택 (Execution Context Stack)

![](https://velog.velcdn.com/images%2Fggong%2Fpost%2F899a0b06-c594-43e0-9a0e-7a41bead87ca%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-06-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%209.48.10.png)

실행 컨텍스트가 생성되면 흔히 콜 스택(Call Stack)이라고도 불리는 실행 컨텍스트 스택에 쌓이게 된다.
`GEC`는 코드를 실행하기 전에 쌓이고 모든 코드를 실행하면 제거된다. `FEC`는 **호출**할 때 쌓이고 **호출**이 끝나면 제거된다.

### 동작과정

1. 처음 자바스크립트 코드를 실행하는 순간 `전역 컨텍스트`가 콜 스택에 담긴다.
   최상단의 공간은 코드 내부에서 별도의 **실행 명령이 없어도 브라우저에서 자동으로 실행**하므로 자바스크립트가 실행되는 순간 `전역 컨텍스트`는 활성화된다고 볼 수 있다.
   `전역 컨텍스트`는 애플리케이션이 종료될 때(웹 페이지에서 나가거나 브라우저를 닫을 때)까지 유지된다.

2. `foo()` 함수가 호출되면, 자바스크립트는 `foo()` 함수에 대한 환경 정보를 수집해서 새로운 **실행 컨텍스트를 생성**한 후, 전역 컨텍스트 위에 쌓는다.

3. `foo()` 함수가 실행되다가 내부 함수 `bar()`를 만나면, 자바스크립트는 `bar()` 함수의 **실행 컨텍스트를 생성**한다. 이 실행 컨텍스트는 스택의 최상단에 쌓인다.

4. 최상단에 쌓인` bar()` 함수가 실행을 종료하면 `bar()` 함수에 의해 만들어진 **실행 컨텍스트는 콜 스택에서 제거**된다.

5. `foo()` 함수가 실행을 종료하면 `foo()` 함수에 의해 만들어진 **실행 컨텍스트는 콜 스택에서 제거**된다.

스택 구조를 생각해보면, 한 실행 컨텍스트가 콜 스택의 맨 위에 쌓이는 순간이 곧 현재 실행할 코드에 관여하게 되는 시점임

## ⭐️ 실행 컨텍스트의 구성

실행 컨텍스트는 다음과 같은 구성요소를 갖는다.

![](https://velog.velcdn.com/images%2Fggong%2Fpost%2F9199d986-67ea-497b-9259-0351b10b3a7c%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-06-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2010.18.29.png)

**VariableEnvironment** : 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보. **선언 시점**의 `LexicalEnvironment`의 스냅샷으로, 변경사항은 반영되지 않음
◦ environmentRecord
◦ outer-EnvironmentReference

**LexicalEnvironment** : 처음에는 VariableEnvironment와 같지만 **변경사항이 실시간으로 반영**됨
◦ environmentRecord
◦ outer-EnvironmentReference

**ThisBinding** : this 식별자가 바라보고 있는 대상 객체

### ✨ Lexical Environment , VariableEnvironment

**Variable Environment** 와 **Lexical Environment** 는 실행 컨텍스트에서 변수의 참조들을 기억하는 환경,
**Variable Environment** 컨텍스트 최초 실행 시점의 스냅샷을 유지하는 반면, **Lexical Environment**에는 변경사항이 반영된다는 것

**Variable Environment**는 **Lexical Environment**와 동일한 성격을 띠지만,
`var` 로 선언된 변수만 저장한다는 점에서 다르다. 즉, `Lexical Environment`는 `var` 로 선언된 변수를 제외하고 나머지(`let` 으로 선언되었거나 함수 선언문)를 저장

#### ✨ **EnvironmentRecord** 와 **OuterEnvironmentReference**

![](https://velog.velcdn.com/images%2Fggong%2Fpost%2F59823b69-9b96-4970-9bcc-f42de3fb87bd%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-06-02%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%2012.30.07.png)

이 두 환경의 내부는 다시 **EnvironmentRecord** 와 **OuterEnvironmentReference** 로 구성

- EnvironmentRecord : 컨텍스트와 관련된 코드의 **식별자 정보**들이 저장됨
- OuterEnvironmentReference : 호출된 함수가 선언될 당시의 **Lexical Environment를 참조하는 포인터**로, 스코프 체인을 가능하게 함

> 식별자 정보: 매개변수 식별자, 선언된 함수, var로 선언된 변수의 식별자 등

### ✨ this 바인딩

this의 바인딩은 실행 컨텍스트가 생성될 때마다 this 객체에 어떻게 바인딩이 되는지를 나타낸 것이다.

> **ES6부터** this의 바인딩이 `LexicalEnvironment` 안에 있는 `EnvironmentRecord` 안에서 일어난다는 사실을 기억해두도록 하자.

- **GEC의 경우**
  - strict mode라면 `undefined` 로 바인딩된다.
  - 아니라면 글로벌 객체로 바인딩된다. (브라우저에선 window, 노드에선 global)
- **FEC의 경우**
  - 해당 함수가 어떻게 호출되었는지에 따라 바인딩된다.

## ⭐️ 실행컨텍스트 과정 - 요약

![0](https://velog.velcdn.com/images/dlwoxhd/post/1a472a58-68ef-48f2-86d9-ab9649f3a1db/image.png)
![1](https://velog.velcdn.com/images/dlwoxhd/post/2d60cbd7-2dea-47a8-bce9-18ef6a280554/image.png)
![2](https://velog.velcdn.com/images/dlwoxhd/post/ef99c10b-0032-47cc-8031-16102f67c025/image.png)
![3](https://velog.velcdn.com/images/dlwoxhd/post/a0eec4ed-9aab-48cf-b5a4-8128e60afbd6/image.png)
![4](https://velog.velcdn.com/images/dlwoxhd/post/0e5fbef7-33b8-4030-ac00-d46b4e05bca5/image.png)
![5](https://velog.velcdn.com/images/dlwoxhd/post/400ffff5-fd19-45ac-97d4-cb9672b7674b/image.png)

---

[생활코딩 - Exectution context](https://www.youtube.com/watch?v=QtOF0uMBy7k&t=334s&ab_channel=%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9)
[테크톡 - 실행 컨텍스트](https://www.youtube.com/watch?v=EWfujNzSUmw&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech)
[제로초 - 실행 컨텍스트](https://www.zerocho.com/category/JavaScript/post/5741d96d094da4986bc950a0)
[velog-실행 컨텍스트란 무엇인가요?](https://velog.io/@edie_ko/js-execution-context)
[velog-자바스크립트의 실행 컨텍스트 (execution context)](https://velog.io/@ggong/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-execution-context)

---

[더 공부해 볼 것 - 실행 컨텍스트 실행과정](https://velog.io/@yejineee/%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%9D%98-%EC%83%9D%EC%84%B1-%EA%B3%BC%EC%A0%95)
