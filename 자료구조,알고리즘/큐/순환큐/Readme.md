# 순환 큐

순환 큐는 마지막 요소가 첫 번쨰 요소에 연결되는 **일반 큐** 의 확장 버전이다.

![순환대기열표현](https://github.com/Jae-hong-lee/TIL/assets/72030487/06cfdb8e-15e6-4862-b69d-b9a501a4c653)

위와 같이 원고 같은 구조를 형성하게 된다.

순환 큐는 일반 큐의 주요 제한 사항을 해결하는데, 일반 큐에서는 삽입과 삭제 후에 사용할 수 없는 빈 공간이 생긴다.

![일반 큐의 제한](https://github.com/Jae-hong-lee/TIL/assets/72030487/d477bffc-d40a-4625-b595-94e04bfd06af)

여기서 인덱스 0, 1 은 큐를 재설정(모든요소삭제)한 후에만 사용할 수 있다.
이렇게 하면 대기열의 실제 크기가 줄어들게된다.

## 순환 큐 작동방식

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/61cc0b2b-7e3f-4da7-8781-0bfe1969f173)
