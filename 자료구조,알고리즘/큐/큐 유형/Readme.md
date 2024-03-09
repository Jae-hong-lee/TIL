# 대기열에 유형

큐에는 4가지 유형이 있다.

- 단순 대기열
- 순환 대기열
- 우선순위 대기열
- 이중 종료 대기열

---

### 단순 큐 (Simple Queue)

단순 큐엣는 삽입이 뒤쪽에서 발생하고 제거가 앞쪽에서 발생.
FIFO(선입선출) 규칙을 엄격하게 따른다.
![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/0a08cc5a-b264-4b26-a878-317a1ec30316)

### 순환 큐 (Circular Queue)

순환 큐에서 마지막 요소는 순환 링크를 만드는 첫번째 요소를 가리키게 된다.

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/131cdfb9-987c-4e82-91a8-5a16a7b40b2e)

단순 큐에 비해 순환 큐의 주요 장점은 메모리 활용도가 더 높다는 것.
마지막 위치가 가득차고 첫 번째 위치가 비어 있으면 첫 번째 위치에 요소를 삽입할 수 있다. 단순 큐에서는 이 작업을 수행할 수 없음.

### 우선순위 큐 (Priority Queue)

우선순위 큐는 각 요소가 우선순위와 연관되어 있고 우선순위에 따라 서비스되는 특별한 유형의 큐이다. 동일한 우선순위를 가진 요소가 발생하면 대기열의 순서에 따라 서비스가 된다.
![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/17311774-400c-45cd-b3e3-fac9131d3806)

삽입은 값의 도착에 따라 발생하고 제거는 우선순위에 따라 발생한다.

### 이중 종료 큐 \*데큐 (Double Ended Queue)

이중종료(Deque)큐 에서는 요소 삽입 및 제거가 전면 또는 후면에서 수행될 수 있다. 따라서 FIFO 규칙을 따르지 않는다.
![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/f5237554-e1ea-494d-b716-8378e326f41f)
