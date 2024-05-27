# 연결리스트 (Linked List)

연결된 목록 일련의 연결된 노드를 포함하는 선형 데이터 구조.
각 노드에는 다음 노드의 데이터와 주소가 저장되게 된다.

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/6941cfd8-8bcc-49f6-b2a1-54d26abfcdec)

연결리스트는 싱글 연결리스트(Singly Linked List), 더블 연결리스트(Doubly Linked List), 원형 연결리스트(Circular Linked List)가 있다.

### 싱글 연결리스트 (Singly Linked List)

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/955f3291-7bf5-4351-b250-10ecd395355b)

연결리스트의 각 노드가 어떻게 표현되는 살펴보면

- 데이터의 항목
- 다른 노드의 주소

크게 이렇게 두 가지로 구성되고, 데이터 항목과, 다음 노드의 주소를 표현할 수 있고 이것을 구조체로 래핑을 한다는 것이다.

```c
// 연결된 목록 노드의 구조를 이해하는 것이 이를 이해하는 것에 핵심!
struct node
{
  int data;
  struct node *next
  // 포인터 next = 가르키는 주소
}
```

각 구조체 노드에는 데이터 항목과 다른 구조체 노드에 대한 포인터가 있는 것.
이것이 어떻게 작동하는지 이해하기 위해 세 가지 항목이 있는 간단한 연결 리스트를 만들어 보자.

```c
/* 노드 초기화 */
struct node *head;
struct node *one = NULL;
struct node *two = NULL;
struct node *three = NULL;

/* 메모리 할당 */
one = malloc(sizeof(struct node));
two = malloc(sizeof(struct node));
three = malloc(sizeof(struct node));

/* 데이터 값 할당 */
one->data = 1;
two->data = 2;
three->data=3;

/* 노드 연결 */
one->next = two;
two->next = three;
three->next = NULL;

/* Save address of first node in head */
// 첫 번째 노드의 주소를 Head에 저장합니다.
head = one;
```

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/de48c67d-2aac-4c46-81ee-c9b2c59afa38)

연결리스트의 힘은 연결을 끊고 다시 연결하는 능력에서 비롯된다.
예를 들어 "1"과 "2"사이에 요소 "4"를 넣으려는 경우 다음 단계를 거치게 된다.

1. 새 구조체 노드를 생성하고 여기에 메모리 할당
2. 데이터 값 "4" 추가
3. 데이터 값으로 "2" 를 포함하는 구조체 노드에 대한 다음 포인터를 가리키게 한다.
4. "1"의 다음 포인터를 방금 생성한 노드로 변경

### 파이썬으로 연결리스트 구현

```py
# Linked list implementation in Python
class Node:
    # Creating a node
    # 노드 만들기
    def __init__(self, item):
        self.item = item
        self.next = None


class LinkedList:

    def __init__(self):
        self.head = None


if __name__ == '__main__':

    linked_list = LinkedList()

    # Assign item values
    # 항목 값 할당
    linked_list.head = Node(1)
    second = Node(2)
    third = Node(3)

    # Connect nodes
    # 노드연결
    linked_list.head.next = second
    second.next = third

    # Print the linked list item
    # 연결리스트 값 출력
    while linked_list.head != None:
        print(linked_list.head.item, end=" ")
        linked_list.head = linked_list.head.next

```
