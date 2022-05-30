---
layout: single
title: "C# LinkedList 구현"
categories: StudyCSharp
tag: [Study, C#, DataStructure]
toc: true
toc_sticky: true
---

# Description

자료구조 중 하나인 양방향 LinkedList를 C#으로 구현해 볼것이다. <br>
Unity에 연동하여 출력하였다.

![Desc](https://user-images.githubusercontent.com/97664446/170868138-ece0f830-2fa5-4494-ad9c-cc7bfdd03a17.PNG)

> Arr와 LinkedList의 차이
- 주소의 연속성 (Arr O, List X)
- Arr는 접근 속도가 빠름
- List는 추가, 삽입, 삭제에 용이함

> 이 정도로 볼 수 있겠다. <br>

# Cord
### Class
```c#
// 제네릭 형식의 Node Class 선언
public class Node<T>
{
    // Node가 가지는 값
    public T data;
    // 다음 Node의 주소
    public Node<T> next = null;
    // 이전 Node의 주소
    public Node<T> prev = null;
}


// 제네릭형식으로 class 선언
public class LinkedList<T>
{
    // 리스트의 첫 번째 Node 
    public Node<T> head = null;

    // List의 길이
    public int Count { get; private set; }
}
```
LinkedList class와 Node class 제네릭 <T> 형식으로 선언한다. <br>
Node에는 (값, 다음 Node, 이전 Node)를 가지게 한다. <br> <br>

LinkedList에는 (첫 번째 Node가 되는 head, 현재 길이를 나타내는 Count)를 프로퍼티로 선언하였다. <br>

***

### Add() & PrintAll()
```c#
// 노드 넣기
public void Add(T _data)
{
    if (Count == 0)
    {
        // 맨 처음 Add할때 Head 지정
        head = new Node<T>();
        head.data = _data;
    }
    else
    {
        // 두번째 부터 다음 Node와 연결 시켜주기
        Node<T> mid = head;

        // next 2 (count >= 3) 부터 for문을 통해 Node를 찾는다
        for (int idx = 0; idx < Count - 1; idx++)
        {
            mid = mid.next;
        }
        mid.next = new Node<T>();
        mid.next.data = _data;  // 다음 Node 연결
        mid.next.prev = mid;    // 이전 Node 연결
    }

    Count++;
}

// List의 모든 Node 출력하기
public void PrintAll()
    {
        // 예외처리
        if (Count == 0)
        {
            Debug.LogError("Empty List");
            return;
        }

        Node<T> mid = head;

        while (mid != null)
        {
            Debug.Log(mid.data);
            mid = mid.next;
        }
    }
```

이제 List에 요소들을 추가하기 위한 Add()와 출력을 위한 PrintAll()을 만들어 보았다 <br>


<img align='' src="https://user-images.githubusercontent.com/97664446/170860526-bd6b6f3c-a5b4-45c2-abb9-85ae157e1a8e.PNG" width="350" height="400"> <img align='' src="https://user-images.githubusercontent.com/97664446/170860522-adef187a-38ad-4a7d-ae7a-0da910461565.PNG" width="450"> <br> <br>

<img align='' src="https://user-images.githubusercontent.com/97664446/170860527-4f3e42c1-4a9b-43f8-bf06-c84975c84fa4.PNG" width="350" height="400"> <img align='' src="https://user-images.githubusercontent.com/97664446/170860525-226dbb57-c306-4827-9ae5-d3040241ac3f.PNG" width="450">


![Result0](https://user-images.githubusercontent.com/97664446/170861011-cadf9749-8541-49f8-bd11-6726dcd1a3bc.PNG)

물론, 조사식에서 list 내부를 들여다 보면 데이터들이 이렇게 잘 들어와 있다. <br>

***

### GetData()
```c#
if (Count <= _idx)  // 예외처리
{
    Debug.LogError("list index out of range"); 
    return default(T);  // 0을 반환
}
else // 지정 _idx의 data 반환
{
    Node<T> mid = head;

    // _idx의 Node 찾기
    for (int idx = 0; idx < _idx; idx++)
    {
        mid = mid.next;
    }

    // 해당 Node의 data 반환
    return mid.data;
}
```

<img align='' src="https://user-images.githubusercontent.com/97664446/170861736-a33a2de0-215a-443a-adfb-3ab67a6998dd.PNG" width="350" height="400"> <img align='' src="https://user-images.githubusercontent.com/97664446/170861733-9f47751e-f09f-44ef-8149-e21d2eb0c403.PNG" width="450"> <br> <br>

![Result3-2](https://user-images.githubusercontent.com/97664446/170861807-7a56be06-406d-4b4e-9a0a-09429d3cb85d.PNG)

예외처리도 아주 잘 되었다. <br>

***

### RemoveLast() & Remove()

```c#
// 마지막 Node 제거
public void RemoveLast()
{
    // 예외처리
    if (Count <= 0) 
    {
        Debug.LogError("List index out of range"); 
        return;
    }

    if (Count == 1) // Node(Head) 가 단 하나 있을 경우
    {
        head.data = default(T);
        head = null;
    }
    else // Node가 2개 
    {
        Node<T> last = head;

        // 3개이상이면 for문으로 Node 탐색
        for (int idx = 0; idx < Count - 2; idx++)
        {
            last = last.next;
        }

        last.next.data = default(T);
        last.next.prev = null;
        last.next = null;
    }

    Count--;
}

// 지정 Node 제거
public void Remove(int _idx)
{
    if (_idx == 0)  // 제거할 Node가 첫 번째 Node 일경우
    {
        // head 제거 전 다음 Node를 미리 저장
        Node<T> headNext = head.next;

        head.data = default(T);
        head = null;

        // 저장한 다음 Node를 head에 대입
        head = headNext;
        head.prev = null;
    }
    else
    {
        Node<T> target = head;  // 제거할 Node
        Node<T> targetNext;     // 제거할 Node의 다음 Node
        Node<T> targetPrev;     // 제거할 Node의 이전 Node

        // 제거할 Node 탐색
        for (int idx = 0; idx < _idx - 1; idx++)
        {
            target = target.next;
        }
        targetNext = target.next.next;
        targetPrev = targetNext.prev;

        // target Node 제거
        target.next.data = default(T);
        target.next.prev = null;
        target.next = null;

        // 끊어진 Node 재연결
        target.next = targetNext;
        target.next.prev = targetPrev;
    }

    Count--;
}
```
 <img src="https://user-images.githubusercontent.com/97664446/170864761-45b0169e-7cc6-477d-a218-095cc8f45dd8.PNG" width="350" height="400">  <img src="https://user-images.githubusercontent.com/97664446/170864759-1c7f877f-bd09-4ce7-8e44-6757fc1092c7.PNG" width="350"> <br> <br>

<img src="https://user-images.githubusercontent.com/97664446/170864762-8b509422-f325-4b54-a178-0d2f148997b0.PNG" width="350" height="400"> <img src="https://user-images.githubusercontent.com/97664446/170869024-30a593ba-3a53-4070-aac3-184dd196fae9.PNG" width="450">

이번에 추가한 것은 마지막 Node제가와 지정 index의 Node 제거 이다. <br>

***

### Insert()
```c#
// _idx에 Node 추가하기
public void Insert(int _idx, T _value)
{
    // 예외 처리
    if ( Count < _idx) 
    { 
        Debug.LogError("List index out of range"); 
        return; 
    }

    if (_idx == 0)   // 맨 처음 idx에 삽입 할때
    {
        // 새로운 Node 생성
        Node<T> newHead = new Node<T>();
        newHead.data = _value;

        // 기존의 head를 temp에 저장
        Node<T> temp = head;
        temp.prev = newHead;

        // 새로 추가된 Node를 head로 대입하고 temp와 연결
        head = newHead;
        newHead.next = temp;
    }
    else  // _idx가 1 이상일 경우
    {
        Node<T> mid = head;
        Node<T> newNode;

        for (int idx = 0; idx < _idx - 1; idx++)
        {
            mid = mid.next;
        }

        // 새로운 Node와 value 추가
        newNode = new Node<T>();
        newNode.data = _value;
        newNode.prev = mid;

        // 새로 추가된 Node와 뒷 Node 연결
        newNode.next = mid.next;
        newNode.next.prev = newNode;

        // 새 Node와 앞 Node 연결
        mid.next = newNode;        
    }

    Count++;
}
```
<img src="https://user-images.githubusercontent.com/97664446/170867114-90a50add-5a0d-4a9c-a680-1f223ece240f.PNG" width="350" height="400"> <img src="https://user-images.githubusercontent.com/97664446/170867111-9b496b1d-9369-4710-aa30-a65523a26708.PNG" width="450">

지정 Index에 Node 추가하기 완성 ~! <br>
전체 코드는 다른 Repositoyu에 업로드 할 것이다. <br>
다음은 LinkedList를 이용한 HashTable을 포스팅 할거다 .<br>