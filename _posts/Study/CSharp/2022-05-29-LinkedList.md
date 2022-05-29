---
layout: single
title: "LinkedList 구현"
categories: StudyCSharp
tag: [Study, C#, DataStructure]
toc: true
toc_sticky: true
---

자료구조 중 하나인 양방향 LinkedList를 C#으로 구현해 볼것이다. <br>
Unity에 연동하여 출력하였다.

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

```c#
// 노드 넣기
public void Add(T _data)
{
    if (Count == 0)
    {
        // 맨 처음 Set할때 Head 지정
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
        mid.next.data = _data;

        // 이전 Node 연결
        mid.next.prev = mid;
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

<table>
  <tr>
    <td><img alt="" src="https://user-images.githubusercontent.com/97664446/170860526-bd6b6f3c-a5b4-45c2-abb9-85ae157e1a8e.PNG" /></td><td><img alt="" src="https://user-images.githubusercontent.com/97664446/170860522-adef187a-38ad-4a7d-ae7a-0da910461565.PNG" /></td>
  <tr>
  <tr>
    <td><img alt="" src="https://user-images.githubusercontent.com/97664446/170860527-4f3e42c1-4a9b-43f8-bf06-c84975c84fa4.PNG" /></td><td><img alt="" src="https://user-images.githubusercontent.com/97664446/170860525-226dbb57-c306-4827-9ae5-d3040241ac3f.PNG" /></td>
  <tr>
</table>

***