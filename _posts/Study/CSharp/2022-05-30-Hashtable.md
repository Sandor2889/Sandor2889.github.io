---
layout: single
title: "C# Hashtable 구현"
categories: StudyCSharp
tag: [Study, C#, DataStructure]
toc: true
toc_sticky: true
---


이번에는 Hashtable을 C#으로 구현해 볼것이다. <br>
이전에 만들었던 LinkedList를 이용하여 만들것이다. <br>
마찬가지로 Unity에 연동하여 출력하였다.

# Description
Hashtable은 배열로 담기엔 무수히 많은 key를 적은 공간에 담기위해 사용한다. <br>
예를 들면 주민등록번호의 경우 13자리인 만큼 배열로 만들면 '10^13'으로 엄청나게 많은 공간을 필요하게 되지만 <br>
Hashtable을 이용하여 이를 줄일 수 있게 된다. <br>
이전에 만들었던 LinkedList의 Node에서 'string key'를 추가하여 GetHashCord()로 index를 받아 저장한다. <br>

![Desc](https://user-images.githubusercontent.com/97664446/171030519-d542181d-126e-434e-8f6a-ca602075e662.PNG) <br>

이러한 형태로 만들고자 한다. <br>


# Cord
### Class
```c#
public class Hashtable<T>
{
    // 길이 기본 값 = 16
    private const int HASH_TABLE_SIZE = 16;
    // 이 테이블의 총 길이
    private int hashTableSize;

    // LinkedList를 배열로 만듬
    private LinkedList<T>[] listBuckets;

    // 생성시 길이 셋팅 했을 경우
    public HashTable(int _size)
    {
        hashTableSize = _size;
        listBuckets = new LinkedList<T>[hashTableSize];   // LinkedList 형태
    }

    // 생성시 길이 셋팅을 하지 않으면 default 값
    public HashTable()
    {
        hashTableSize = HASH_TABLE_SIZE;
        listBuckets = new LinkedList<T>[HASH_TABLE_SIZE];   // LinkedList 형태
    }
}
```
LinkedList로 된 hashtable을 생성자를 통해 size를 할당하였다 <br>


***

### GetHashCode() & Put() 
```c#
// GetHashCode 재정의
private int GetHashCode(string _key)
{
    return Mathf.Abs(_key.GetHashCode() % hashTableSize);
}

// Hashtable에 Key와 valude 추가
public void Put(string _key, T _value)
{
    // string key를 int index 형태로 변환
    int index = GetHashCode(_key);
    if (listBuckets[index] == null)
    {
        listBuckets[index] = new LinkedList<T>();
    }
    listBuckets[index].Add(_key, _value);
}

```
![Test0](https://user-images.githubusercontent.com/97664446/171031786-2679c51e-38cc-4b88-b973-ab2e2a08732e.PNG)
5가지를 Put() 하였다, 결과는? 

![Result0](https://user-images.githubusercontent.com/97664446/171032502-30a338ef-3bad-41a4-be49-980d0d7d5928.PNG)
4개만 보인다, 각각 열어보자

![Result0-2](https://user-images.githubusercontent.com/97664446/171032504-b141bb31-899f-4483-8ae2-14250b31cd9f.PNG)
짜잔~ [2] = 체리, [4] = 복숭아, [6] = 사과, [10] = 바나나, 딸기 <br>
이렇게 [10]에 두개가 들어간 것을 볼 수가 있다. <br>

***

### GetData()
```c#
public object GetData(string _key)
{
    object data = "This key does not exist";   // data 값 반환
    int index = GetHashCode(_key);  // _key가 가진 HashTable의 index값 저장
    LinkedList<T> currentList = listBuckets[index];  // _key가 있는 HashTable의 주소를 저장  

    // 예외처리1 _key가 없을 경우
    if (currentList == null) { return data; }

    Node<T> current = currentList.head;

    // Node가 한개 이상 일때 탐색
    for (int idx = 0; idx < currentList.Count; idx++)
    {
        // _key 와 현재 Node의 key가 같으면 나가기
        if (_key == current.key)
        {
            data = current.data;
            break;
        }

        // 예외처리2: _key가 다르지만 같은 index를 가질 수 있기 때문에 한번더 확인을 해줘야 한다
        if (idx == currentList.Count - 1) { return data; }
        current = current.next;
    }

    return data;
}
```

key를 추적하여 key가 가진 data를 가져오는 GetData()를 구현 해보았다 <br>

![Test1](https://user-images.githubusercontent.com/97664446/171034471-410e94c6-bf44-43c7-b3da-1a66e698290a.PNG) ![Result1](https://user-images.githubusercontent.com/97664446/171034475-aefcf2b6-f81d-4da9-825b-1262976cd641.PNG) <br>

바나나의 data와 존재하지 않는 수박의 data가 출력되었다 <br>
앗 그리고 올바른 영어 표현은 이것 이였다... <del>"This Key has not exit" </del> <br>
영어 기본이라도 하자... "This key does not exist" <br>>

***

### Remove()
```c#
public void Remove(string _key)
{
    int index = GetHashCode(_key);  // _key가 가진 HashTable의 index값 저장
    LinkedList<T> currentList = listBuckets[index];  // _key가 있는 HashTable의 주소를 저장  
    Node<T> target = currentList.head;    // 지울 대상 target

    if (currentList.Count <= 0) // 예외처리
    {
        Debug.LogWarning("Has not exit " + _key);
        return;
    }
    else if(currentList.Count == 1) // head 단 하나 존재 할 경우
    {
        if (_key == target.key)
        {
            currentList.RemoveLast();
            listBuckets[index] = null;
        }
    }
    else   // Node가 두 개 이상 일때
    {
        for (int idx = 0; idx < currentList.Count - 1; idx++)
        {
            // _key와 현재 Node의 key가 같으면 나가기
            if (_key == target.key)
            {
                currentList.Remove(idx);

                break;
            }

            // _key가 존재하지 않을 경우
            if (idx == currentList.Count - 1)
            {
                Debug.LogWarning("Has not exit " + _key);
                return;
            }
        }
    } 
}
```
![Test2](https://user-images.githubusercontent.com/97664446/171042018-4752505b-f6bb-4713-9fe3-53e690c00cdc.PNG)

복숭아, 사과, 바나나의 key를 추적하여 해당 Node를 제거하고 Node가 하나라도 존재하지 않으면 <br>
해당 hashtable[idx]는 null 처리 할 것이다. <br>

![Result2](https://user-images.githubusercontent.com/97664446/171042023-abf92f7d-f9d6-4870-9d55-ac2ed471249d.PNG)

이렇게 [2]체리와 [10]딸기 만이 남은 것을 볼 수 있다.

***

이로써 Hashtable도 구현 해보았다 <br>
LinkedList에 이어 하나하나 포스팅하려니 정~말 힘들다 <br>
다른 사람들은 어떻게 수 많은 글들을 올릴 수 있었던건지... 대단하다고 생각한다 <br>
블로거는 무지 어렵고, 시간도 많이들고, 엄청난 인내심이 필요 하다고 느낀다.


