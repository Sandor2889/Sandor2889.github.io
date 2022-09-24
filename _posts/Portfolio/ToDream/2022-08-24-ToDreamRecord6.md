---
layout: single 
title: "To-dream : 기록 제 6장 : WorldMap"
categories: ToDream
tag: Portfolio
---

월드맵 기능을 만들어 보았다.  
M키를 눌러 현재 나의 위치를 확인하고 퀘스트의 목표지점을 확인 하도록 한다.  
그리고 마킹 기능도 추가 할 것이다.  

미니맵 관련으로는 관련영상이 많아 다양한 방식으로 만들어 볼 수 있겠는데  
월드맵은 자료가 거의 없는것 같다.  

실제 맵 크기와 지도의 축척 크기 비율을 고려해 좌표 값을 계산하는...  
그럴듯 해보여서 일단 무작정 부딪히는 도중!    
팀원분의 아이디어를 빌려 꼼수로 구현하고자 하였다.  
  

얼마 후 만들었지만 방식이 미니맵을 만들 듯이 카메라를 위에서 아래로 맵 전체를 그려내니까  
카메라에 렌더링되는 것들이 너무 많아 렉이 심하게 걸렸다. 그래서 실제 지도처럼 축척 비율을  
고려하여 만들어 보기로 하였다.  


# 맵 이미지와 축척 비

우선 전체 맵이 나올 이미지가 필요하다.
캐릭터의 원점과 카메라의 원점을 맞춘다 -> (0, 0, 0)

카메라를 orthographic로 셋팅하고 우리 맵이 x,z (-1500 ~ 1500)이 나오도록 한 화면에 담아 스샷을 찍었다.

![WorldMap](https://user-images.githubusercontent.com/97664446/191030920-e47c12d7-dda8-413d-86ff-cbba87094c90.PNG)


UI 맵의 크기는 1000으로 실제맵과 1:3비율이다.  
그러면 Player의 위치 / 3을 하면된다. (참고로 나는 * 0.334f로 하였다.)  

# Player 위치 업데이트

월드 맵을 켰을때 Update()를 사용해 위치값을 계속 받는 것보다 코루틴을 이용하여  
키면 시작하고 끄면 멈추도록 만들었다.  

```c++
[Header("[Player]")]
[SerializeField] private Transform _player;     // 실제 Player 위치
[SerializeField] private Image _playerIcon;     // 맵의 Player 이미지

private const float _scaleFactor = 0.334f;      // 축척비
private Coroutine _coroutinMap;                 // 현재 실행중인 코루틴     

private void OnEnable()
{
    _coroutinMap = StartCoroutine(CUpdateMap());
}

private void OnDisable()
{
    StopCoroutine(_coroutinMap);
}

private void SetPlayerPos()
{
    _playerIcon.rectTransform.anchoredPosition = new Vector2(_player.position.x * _scaleFactor, _player.position.z * _scaleFactor);
}

private IEnumerator CUpdateMap()
{
    while (true)
    {
        SetPlayerPos();
        yield return null;
    }
}
```

UI의 이미지 위치를 Canvas내에서 이동 시킬땐 anchoredPosition을 이용해야 한다.  

![PlayerPosUpdate](https://user-images.githubusercontent.com/97664446/191030910-c5cf3048-49e6-4372-9be0-ecae40b48b3c.gif)

짠~  Player의 위치가 Map을 키는동안 잘 업데이트가 되는 것을 볼 수 있다.  

# NPC와 QuestTarget

NPC와 QuestTarget은 고정된 위치이다. 그래서 각각의 위치를 직접 잡아 주었다.  
![NPCAndQuestTarget](https://user-images.githubusercontent.com/97664446/191268236-f381ac58-b1d2-4bb8-9652-ec1a3deba1a7.PNG)  

NPC는 NPCMakerUI에서 모든 NPC 데이터를 가지고 있다. 그리고 각 NPC의 퀘스트 상태에따라 이미지를 바꿔주고자 한다.  
default, avaliable, inProgress, completed의 상태가 있다.  
OnEnable()에 다음과 같은 메서드를 추가한다.  

```c++
[Header("[NPC]")]
[SerializeField] private Sprite _default;
[SerializeField] private Sprite _avaliable;
[SerializeField] private Sprite _inProgress;
[SerializeField] private Sprite _completed;

public void NPCUpdate()
{
    NPCMarkerUI npcMarkerUI = UIManager._Instance._NPCMarkerUI;

    for (int i = 0; i < npcMarkerUI._Npcs.Length; i++)
    {
        if (npcMarkerUI._Npcs[i]._CurrentQuest == null || npcMarkerUI._Npcs[i]._CurrentQuest._questState == QuestState.Unvaliable)
        {
            npcMarkerUI._Npcs[i]._myImage.sprite = _default;
        }
        else if (npcMarkerUI._Npcs[i]._CurrentQuest._questState == QuestState.Avaliable)
        {
            npcMarkerUI._Npcs[i]._myImage.sprite = _avaliable;
        }
        else if (npcMarkerUI._Npcs[i]._CurrentQuest._questState == QuestState.Accepted)
        {
            npcMarkerUI._Npcs[i]._myImage.sprite = _inProgress;
        }
        else if (npcMarkerUI._Npcs[i]._CurrentQuest._questState == QuestState.Completed)
        {
            npcMarkerUI._Npcs[i]._myImage.sprite = _completed;
        }
    }
}
```

QuestTarget은 Quest 수락이 되면 각각의 이미지들을 활성화 시켜줘야한다.  
QuestTarget을 따로 저장하고 있지 않기 때문에 새로 만들어 주었다.  
그리고 각각 QuestTarget에 대응되는 Image[]도 같이 만들어 준다.  
마찬가지로 OnEnable()에 메서드를 추가한다.  

```c++
 [Header("[QuestTarget]")]
[SerializeField] private GameObject _targetParent;
[SerializeField] private QuestTargetMarker[] _questTargets;     // _targetImage와 순서 맞출 것
[SerializeField] private Image[] _targetImage;                  // _questTargets와 순서 맞출 것
[SerializeField] private Sprite _questIcon;

public void QuestTargetUpdate()
{
    for(int i = 0; i < _questTargets.Length; i++)
    {
        if (_questTargets[i].gameObject.activeSelf)
        {
            _targetImage[i].gameObject.SetActive(true);
        }
        else
        {
            _targetImage[i].gameObject.SetActive(false);
        }
    }
}
```

# Name Text

각각의 Icon들이 뭔지 잘 모를 수 있어 Text를 추가 해봤다.  마우스를 해당 Icon에 올리면 텍스트가 보일 것이다.  
각 Icon에 Event Trigger를 달고 Enter일 경우 텍스트를 키고 Exit일 경우 텍스트를 끄는 방식으로 하였다.  
Icon의 name은 IconName을 같이 달아줘서 이름을 넘겨 받는다.  
![NameText](https://user-images.githubusercontent.com/97664446/191268249-a8ab539c-18f5-4675-8089-ae0aaa8c60e6.PNG)


```c++
[SerializeField] private Text _iconName;                            // Icon 이름표
[SerializeField] private Vector2 _offsetPos = new Vector2(0, 30);   // Text offset Pos

public void OnText(IconName iconName)
{
    _iconName.gameObject.SetActive(true);
    _iconName.rectTransform.anchoredPosition = iconName.GetComponent<RectTransform>().anchoredPosition + _offsetPos;
    _iconName.text = iconName._name;
}

public void OffText()
{
    _iconName.gameObject.SetActive(false);
    _iconName.text = " ";
}
```

![Result1](https://user-images.githubusercontent.com/97664446/191268245-1bf9ac38-0250-4ecb-b788-a07c039c38ab.PNG)


# Ping
보통 월드맵 기능중 가고자하는 위치에 Ping을 찍는 기능이 있다.  
거기에 길찾기 시스템이 있지만 우리는 Navigation 기능으로 대체하였으니 건너뛰고 이 Ping 기능을 만들어 보겠다.

1. 일단 맵을 키면 맵 UI 사이즈 안에서 우클릭을 하면 PingIcon을 마우스 위치에 나타나게한다.  
2. 나타나게 했으면 제거도 만들어야한다. 제거는 PingIcon 위에서 다시 한번 우클릭 하는 것이다.  
    그렇다면 if 조건으로 Ping위에 마우스가 있다면 제거, 없다면 나타내기가 될 것이다. 

```c++
[Header("[Ping]")]
[SerializeField] private Image _pingImage;
[SerializeField] private Ping _ping;
[SerializeField] private bool _pointerOnPing;

private void TryOnPing()
{
    // 우클릭 && 맵의 사이즈 (1000x1000) 범위 안에 클릭할 경우
    if (Input.GetMouseButtonUp(1)
        && Input.mousePosition.x >= 460 && Input.mousePosition.x <= 1460 
        && Input.mousePosition.y >= 40 && Input.mousePosition.y <= 1040)
    {          
        if (_pingImage.gameObject.activeSelf && _pointerOnPing)
        {   // 현재 Ping이 있고 마우스 위치가 Ping과 같은 경우
            RemovePing();
        }
        else
        {
            SetPingPos();
        }  
    }
}

private void SetPingPos()
{
    _ping.gameObject.SetActive(true);
    _pingImage.gameObject.SetActive(true);
    _pingImage.rectTransform.position = Input.mousePosition;
}

public void RemovePing()
{
    _pointerOnPing = false;
    _ping.gameObject.SetActive(false);
    _pingImage.gameObject.SetActive(false);
    _iconName.gameObject.SetActive(false);
}
```

이 TryOnPing()을 위에서 코루틴으로 만든 CUpdateMap 코루틴에서 같이 돌려주면 된다.  
그러면 이제 맵에서 보일 Ping 오브젝트를 만들어야 한다.

SetPingPos() 메서드에서 이와 같이 코드를 추가한다.  
```c++
private const float _fallHeight = 0f;
private const int _realScale = 3;

private void SetPingPos()
{
    _ping.gameObject.SetActive(true);
    _pingImage.gameObject.SetActive(true);
    _pingImage.rectTransform.position = Input.mousePosition;

    // Ping 실제 월드공간 위치지정
    float posX = _pingImage.rectTransform.anchoredPosition.x;
    float posZ = _pingImage.rectTransform.anchoredPosition.y;
    _ping.transform.position = new Vector3(posX * _realScale, _fallHeight, posZ * _realScale);
} 
```