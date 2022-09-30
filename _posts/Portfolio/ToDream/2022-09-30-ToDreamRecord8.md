---
layout: single 
title: "To-dream : 기록 제 8장 : 스택형 UI"
categories: ToDream
tag: Portfolio
---

이번에 만들어 볼 것은 스택형 UI이다.  
`LIFO` 방식으로 UI를 킨 순서대로 가장 앞에 그려지고 끌때는 역순으로 꺼진다. (ESC로 종료 할 시)
하지만 이것만 하면 재미없다. 
내가 했던 게임은 ESC로도 UI를 종료하지만 다른 단축키로 해당 UI만 골라 끄는 기능이 있었다.  

# PopupUIBase

우선 유저가 자유롭게 통제가 가능한 UI들은 기본적으로
Open/Close 기능이 있다. 필수적인 요소이기 때문에 추상 메서드로서 정의 할 것이다.

```c#
public abstract class PopupUIBase : MonoBehaviour
{
    public abstract void OpenControllableUI();

    public abstract void CloseControllableUI();
}
```

PopupUIBase를 상속받는 주인공들은 InventoryUI, WorldMapUI, QuestListUI 이다.  
각각  Monobehaviour 대신 PopupUIBase를 상속 받도록 해준다.  
그다음 기존에 있던 코드들을 abstract로 선언한 Open/Close에 대한 메소드에 그대로 넣어 주면된다.

다음은 열려있는 UI들에 대해 관리할 List를 만들어야 한다. 원래라면 Stack을 썼겠지만 내가 만들것은 중간 요소중 제거를
할 필요가 있기 때문에 List로 선언한다.
UIManager에서 관리 하도록 하겠다.

> private List<PopupUIBase> _popupUI = new List<PopupUIBase>();

자 그러면 이러한 동작을 거치게 될 것이다.

1. UI를 연다. -> _popupUI.Add(popupUI); popupUI.Open();
2. UI를 닫는다. -> _popupUI.Remove(popupUI); popupUI.Close();

이 로직을 하나로 묶어 보았다.

```c#
// PopupUI - 열었을 때 단축키로 다시 닫기 기능
private void OpenOrClose(PopupUIBase popupUI)
{
    if (popupUI.gameObject.activeSelf)  // 닫기
    {
        _popupUI.Remove(popupUI);
        popupUI.CloseControllableUI();
    }
    else   // 열기
    {
        _popupUI.Add(popupUI);
        popupUI.OpenControllableUI();
    }
}
```

다음은 ESC에 대한 종료, 단축키로 인한 종료를 따로 만들어줘야 한다.  
우선 Update에서 입력 값을 감지하여 UI를 컨트롤하는 ControlUI()를 만든다.  
그리고 ESC에 대한 조건문을 추가한다.  

```c#
private void ControlUI()
{
    // 인벤토리 창
    if (Input.GetKeyDown(KeyCode.I))
    {
        OpenOrClose(_inventoryUI);
    }

    // 퀘스트 창
    if (Input.GetKeyDown(KeyCode.Q))
    {
        OpenOrClose(_questListUI);
    }

    // 월드 맵
    if (Input.GetKeyDown(KeyCode.M))
    {
        OpenOrClose(_worldMapUI);
    }

    // esc 기능 - 가장 마지막에 킨 UI부터 종료
    if (Input.GetKeyDown(KeyCode.Escape))
    {
        int lastIdx = _popupUI.Count - 1;   // 가장 최근에 Open한 UI 인덱스
        if (lastIdx < 0) { return; }        // 예외처리 - 열려있는 UI가 없을 때

        PopupUIBase lastUI = _popupUI[lastIdx];
        lastUI.CloseControllableUI();
        _popupUI.RemoveAt(lastIdx);
    }
}
```

List로 마지막 요소를 제거 해야하기 때문에 RemoveAt()를 사용한다.  
마지막 인덱스는 (Count-1)가 된다.  

일단 이렇게만 해도 원하던 기능은 동작 하지만 UI의 그려지는 순서를 바꿔줘야한다.  
Canvas 클래스에 sortingOrder라는 프로퍼티가 있다. 숫자가 높을 수록 가장 위에 그려진다.  
그래서 PopupUIBase에 다음과 같이 추가 하겠다.

> public Canvas _canvas;

그리고 UIManager에서 SetOrder() 메서드를 추가 한다.
UI가 추가되거나 제거 될때마다 for문으로 popupUI의 모든 요소에 대해 order값을 재설정해줄 것이다.  

```c#
// PopupUI 순서
private void SetOrder(PopupUIBase popupUI, bool active)
{
    if (active)
    {
        // 켜져있는 모든 UI에대해 재정렬
        for (int i = 0; i < _popupUI.Count; i++)
        {
            _popupUI[i]._canvas.sortingOrder = _popupUI.IndexOf(_popupUI[i]) + 1;
        }
    }
    else { popupUI._canvas.sortingOrder = 0; } 
}
```

그러면 이제 다시 이 메서드를 Open/Close시 호출 하도록 다음과 같이 작성한다.  
```c#
// PopupUI - 열었을 때 단축키로 다시 닫기 기능
private void OpenOrClose(PopupUIBase popupUI)
{
    if (popupUI.gameObject.activeSelf)  // 닫기
    {
        _popupUI.Remove(popupUI);
        SetOrder(popupUI, false);
        popupUI.CloseControllableUI();
    }
    else   // 열기
    {
        _popupUI.Add(popupUI);
        SetOrder(popupUI, true);
        popupUI.OpenControllableUI();
    }
}
```

이제 Canvas 컴포넌트와 Graphic Raycaster 컴포넌트를 추가하면 된다.  
잘 되는지. 확인해보자.

# Result
![PopupUI](https://user-images.githubusercontent.com/97664446/193282736-bfadc310-6491-4bee-89a2-2204136618b2.gif)


