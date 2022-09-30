---
layout: single 
title: "To-dream : 기록 제 9장 : MovableUI"
categories: ToDream
tag: Portfolio
---

이번에는 유저가 UI를 마음대로 이동 가능하게 해주는 기능을 만들었다. 
이동 가능한 UI는 인벤토리, 퀘스트 목록 정도가 있다.  

이동하고자 하는  target UI의 Transfrom이 필요 할 것이고 
드래그로 이동하니 IDrag 인터페이스를 받아야한다.  


```c#
public class MovableUI : MonoBehaviour, IBeginDragHandler, IDragHandler
{
    [SerializeField] private Transform _targetTr; // 이동될 UI

    private void Awake()
    {
        // 이동 대상 UI를 지정하지 않은 경우, 자동으로 부모로 초기화
        if (_targetTr == null) { _targetTr = transform.parent; }
    }

    // 드래그 : 마우스 커서 위치로 이동
    void IDragHandler.OnDrag(PointerEventData eventData)
    {
        _targetTr.position = eventData.position;
    }
}
```

![MovableUI](https://user-images.githubusercontent.com/97664446/193302121-ea17e3c6-e819-4439-9589-bebc75790e13.PNG)  
빈 오브젝트 MovablePoint를 생성하여 MovableUI를 붙여넣었다.  
그리고 각각의 UI의 손잡이 역할을 할 곳의 크기에 맞춰준다.  

![MovableUI](https://user-images.githubusercontent.com/97664446/193304075-963da215-81c7-43df-b68d-6ca9de1de012.gif)  
이동은 되지만 UI의 원점이 마우스 위치로 순간이동하여 이상하다.  
offset을 줄필요가 있어보인다.  
1. 드래그를 시작한 위치 벡터와 기준으로 움직인 위치 벡터의 차를 실시간으로 구한다.
2. UI의 위치는 (시작 점 + 벡터의 차)가 될 것이다.

```c#
[SerializeField] private Transform _targetTr; // 이동될 UI

private Vector2 _startingPos;
private Vector2 _moveBegin;
private Vector2 _Offset;

private void Awake()
{
    // 이동 대상 UI를 지정하지 않은 경우, 자동으로 부모로 초기화
    if (_targetTr == null) { _targetTr = transform.parent; }
}

// 드래그 시작 위치 지정
void IBeginDragHandler.OnBeginDrag(PointerEventData eventData)
{
    _startingPos = _targetTr.position;
    _moveBegin = eventData.position;
}

// 드래그 : 마우스 커서 위치로 이동
void IDragHandler.OnDrag(PointerEventData eventData)
{
    _Offset = eventData.position - _moveBegin;
    _targetTr.position = _startingPos + _Offset;
}
```
코드가 이와 같이 추가 되었다. 결과를 확인해보자.

# Result
![Result](https://user-images.githubusercontent.com/97664446/193304082-6cba5181-76bc-43ea-a8ce-04e45b20f399.gif)
