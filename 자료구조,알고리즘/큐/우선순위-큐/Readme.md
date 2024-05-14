# 우선순위 큐

![Priority queue](https://github.com/Jae-hong-lee/TIL/assets/72030487/ba857c47-8a6a-47ee-8572-8ee97bbe94c2)

우선순위 큐는 각 요소가 우선순위 값과 연관되어 있는 특별한 유형의 큐!
큐는 먼저 들어오는 데이터가 먼저 나가는 FIFO 형식의 선형 자료구조이지만, 우선순위 큐는 들어오는 순서에 상관없이 우선순위가 높은 데이터가 먼저 나가는 자료구조이다.

### 우선순위 큐 속성

1. 모든 항목(요소)에는 우선순위를 가지고 있다.
2. 우선 순위가 높은 요소는 우선 순위가 낮은 요소보다 먼저 큐에서 제외.
3. 두 요소의 우선 순위가 같으면 큐의 순서에 따라 제외된다.

### 우선순위 큐 구현

우선순위 큐는 배열, 연결목록, 힙 데이터 구조 또는 이진 검색 트리를 사용하여 구현할 수 있고, 이러한 데이터 구조 중에서 **힙 데이터 구조**는 우선순위 큐를 만들기 가장 효율적이다. (큐 삽입, 삭제 두개의 시간복잡도가 둘다 `O(logN)`이다. )

> ##### [힙데이터](https://www.programiz.com/dsa/heap-sort#heap)
>
> maxHeap: 부모가 자식보다 값이 항상 같거나 커야함. but 마지막 요소는 제외된다.
> minHeap: 부모가 자식보다 값이 항상 같거나 작아야함.
> Heapify: Heapify는 힙 속성을 유지하는 작업을 의미. 위에서 아래로 내려가면서 작업을 한다.

### 우선순위 큐 삽입연산

1. 트리 끝에 새요소 삽입
   ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/8b067b77-d2ae-48ab-8d4a-75acc397263a)
2. 데이터 구조가 변환(Heapify)
   부모노드와 비교하여 힙 속성을 위배하는 경우 부모노드와 값을바꿔준다.
   ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/1fd6e8e7-ba17-41d7-90f3-e703bf30e5c5)

> 전체과정
> ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/b64ee200-8e2e-4447-a0d5-bb0c075d4456)

### 우선순위 큐 삭제연산

1. 삭제할 요소 선택
   ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/e8116cda-8773-4acf-8646-678a6bcb658c)
2. 마지막 요소(노드)로 바꿔준다.
   ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/c0ab8af6-c377-4e48-b03d-d8f750920ac1)
3. 마지막 요소(노드) 제거
   ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/657cb4ac-6573-45e3-81df-5bf2254175b7)
4. 루트 노드부터 Heapify 수행
   루트부터 밀단까지 내려가면서 Heapify 작업을 수행.

> 전체과정
> ![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/f597cbb9-3677-4912-8f0c-b15f7faf06e5)

### 힙의 구현 Python

```py
# Python에서 priority 큐 구현
# 트리를 힙화하는 기능
def heapify(arr, n, i):
    # Find the largest among root, left child and right child
    # 루트, 왼쪽 자식, 오른쪽 자식 중에서 가장 큰 것을 찾기
    largest = i
    l = 2 * i + 1
    r = 2 * i + 2

    if l < n and arr[i] < arr[l]:
        largest = l
    if r < n and arr[largest] < arr[r]:
        largest = r
    # 루트가 가장 크지 않은 경우 스왑 및 계속 힙화
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

# 요소를 트리에 삽입하는 함수
def insert(array, newNum):
    size = len(array)
    if size == 0:
        array.append(newNum)
    else:
        array.append(newNum)
        for i in range((size // 2) - 1, -1, -1):
            heapify(array, size, i)

# 트리에서 요소를 삭제하는 기능
def deleteNode(array, num):
    size = len(array)
    i = 0
    for i in range(0, size):
        if num == array[i]:
            break
    array[i], array[size - 1] = array[size - 1], array[i]
    array.remove(size - 1)
    for i in range((len(array) // 2) - 1, -1, -1):
        heapify(array, len(array), i)

arr = []
insert(arr, 3)
insert(arr, 4)
insert(arr, 9)
insert(arr, 5)
insert(arr, 2)
print ("Max-Heap 배열: " + str(arr))

deleteNode(arr, 4)
print("요소 삭제 후: " + str(arr))
```

### 우선순위 큐 활용!

Some of the applications of a priority queue are:

- Dijkstra의 알고리즘
- 스택 구현을 위해 (for implementing stack)
- 운영 체제의 로드 밸런싱 및 인터럽트 처리용
- 허프만 코드의 데이터 압축용
