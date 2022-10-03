---
layout: single 
title: "To-dream : 기록 제 4.5장 : NPC 상호작용 부터 퀘스트 확인까지 1편"
categories: ToDream
tag: Portfolio
---

# QuestGiver

각각의 를 작성할 때 enum으로된 NPCName을 입력하는 변수가 있다.  
NPC들은 QuestGiver를 가지고 있으며 마찬가지로 어떤 NPC인지 enum 타입으로 가지게 된다.  
그렇다면 QuestManager에 등록된 퀘스트중 NPCName을 가져와보도록 하자.  
```c#
public enum NPCName
{
    Mother,
    Manager,
    President
}

public class QuestGiver : MonoBehaviour
{   
    public NPCName _npcName;
    public List<Quest> _myQuests = new List<Quest>();   // 이 NPC가 가진 퀘스트

    // 각 NPC의 Quest 가져오기
    private void DistributeQuests()
    {
        // QuestManager에서 NPCName이 같은 모든 퀘스트 가져오기
        QuestManager questMgr = QuestManager._Instance;
        _myQuests = questMgr._questContainer.FindAll(x => x._npcName.GetHashCode() == _npcName.GetHashCode());
    }
}
```

그냥 string으로 비교하는 것보다 HashCode로 비교하는 것이 훨씬 빠르다고하여 이렇게 하였다.  

우리는 NPC가 주는 퀘스트를 골라서 받는 기능은 없다. Player가 NPC와 상호작용 한다면 NPC가 가진 첫 번째 퀘스트부터 순차적으로 받을 것이다. 그렇기 때문에 현재 퀘스트를 나타내는 변수를 선언한다.  

```c#
public Quest _CurrentQuest
{
    get
    {
        // 더이상 퀘스트가 없을때
        if (_currentQuestIdx >= _myQuests.Count)
        {
            return null;
        }
                    
        return _myQuests[_currentQuestIdx];
    }
}
```


# NPCMakerUI
각각의 NPC가 가진 퀘스트에 따라 머리위에 마크를 달 것이다.  
조건과 모양은 다음과 같다.  
- 퀘스트가 없거나 수락 불가 : 마크 없음
- 수락 가능 : ?
- 진행중 : 톱니바퀴
- 완료 가능 : ★

NPC를 배열로 저장하고 퀘스트가 완료될때 마다 이벤트로 갱신시켜주겠다.  

```c#
public class NPCMarkerUI : MonoBehaviour
{
    [SerializeField] private QuestGiver[] _npcs;

    public QuestGiver[] _Npcs => _npcs;

    // (int 퀘스트제공자, QuestState 퀘스트 진행상태)
    public void SettingByQuestState(int idx, QuestState questState)
    {
        OnMarker(idx, questState);
    }

    private void OnMarker(int idx, QuestState state)
    {
        QuestGiver giver = _npcs[idx];
        int intState = (int)state - 1;  // 0은 수락 불가능 상태이므로 1부터 시작

        for(int i = 0; i < giver._Markers.Length; i++)
        {
            // 수락 불가 상태나 완료된 상태라면 모든 마커 종료
            if (state == QuestState.Unvaliable || state == QuestState.Done)
            {
                giver._Markers[i].gameObject.SetActive(false);
                continue;
            }

            if (intState == i)
            {
                giver._Markers[i].gameObject.SetActive(true);
            }
            else 
            {
                giver._Markers[i].gameObject.SetActive(false);
            }
        }
    }
}
```

이벤트들은 QuestGiver에서 퀘스트를 Manager에서 가져올때 같이 추가시켜준다.
Quest에서 먼저 이벤트를 선언해준다.

Quest class
```c#
// QuestGiver의 현재 퀘스트 상태에 따른 Marker 업데이트
public System.Action<int, QuestState> _onNPCMarker; 
```

QuestGiver class
```c#
private void DistributeQuests()
{
    // QuestManager에서 NPCName이 같은 것 가져오기
    QuestManager questMgr = QuestManager._Instance;
    _myQuests = questMgr._questContainer.FindAll(x => x._npcName.GetHashCode() == _npcName.GetHashCode());

    if (_myQuests.Count <= 0) { return; }

    // 이벤트 등록
    foreach (var quest in _myQuests)
    {
        quest._onNPCMarker += UIManager._Instance._NPCMarkerUI.SettingByQuestState;
    }
}
```

![NPCMarker](https://user-images.githubusercontent.com/97664446/193474991-3dd437d1-3c1a-4732-b94f-80243c5c7a2e.PNG)  
  
# ObjectInteraction과 InteractionUI

NPC와 대화를 하기 위한 상호작용을 만들어야 한다.
캐릭터에서 정면으로 Ray를 쏘고 충돌체가 Layermask를 이용해 NPC인 것을 골라내자.  
또한, 상호작용 할 다른 오브젝트를 고려하여 enum으로 정의 하였다. (확장성 고려)  
```c#
public enum InteractionObjectType
{
    Default,
    NPC,
    Item
}

public class ObjectInteraction : MonoBehaviour
{
    [SerializeField] private LayerMask _mask;
    private RaycastHit _hit;
    private InteractionObjectType _interObjType;
    private int _npc;

    private void Awake()
    {
        _npc = LayerMask.NameToLayer("NPC");
    }

    private void Update()
    {
        OnInterection();
    }

    private void OnInterection()
    {
        Debug.DrawRay(transform.position + Vector3.up, transform.forward, Color.blue, 1f);
        if (Physics.Raycast(transform.position + Vector3.up, transform.forward, out _hit, 1f, _mask))
        {
            // hit.Layer가 NPC 일때 (다른 타입이 추가 될경우 else if또는 switch로 추가)
            if (_hit.collider.gameObject.layer == _npc)
            {
                _interObjType = InteractionObjectType.NPC;
            }
        }
    }
}
```
Player에 이 스크립트를 붙여주고 Layer를 설정하자.  
![ObjectInteraction](https://user-images.githubusercontent.com/97664446/193472275-9d5de146-bd25-4deb-b02f-34803f43c29f.PNG)
![DrawRay](https://user-images.githubusercontent.com/97664446/193472270-3e9f57e5-f184-45dc-aaac-9290ce3e862e.PNG)  

이러면 일단 NPC에 한해서만 충돌 할 것이다. 그러면 Talk 라는 메세지를 띄워 보도록 하자.  

# InteractionUI

마찬가지로 확장성을 고려해 출력 되어질 text에 상호작용되는 물체에따라 string이 대입 될 것이다.  
현재는 NPC만 있으니 itemText는 직렬화를 안 시켜 놓을 것이다.

```c#
public class InteractionUI : MonoBehaviour
{
    [SerializeField] private string _talkText;  // 인스펙터에서 텍스트 입력
    private string _itemText;
    public Text _text;

    public void OnInterText()
    {
        gameObject.SetActive(true);
    }

    public void OffInterText()
    {
        gameObject.SetActive(false);
    }

    public void SetText(InteractionObjectType interObj)
    {
        switch (interObj)
        {
            case InteractionObjectType.NPC:
                _text.text = _talkText;
                break;
            case InteractionObjectType.Item:
                _text.text = _itemText;
                break;
            default:
                break;
        }
    }
}
```

이제 물체 충돌시 나타낼 텍스트를 만들었으니 다시 ObjectInteraction 스크립트로 돌아가자
대화가 가능한 조건을 만들어 준다. 다음은 Talk가 가능한 조건이다.  

1. interObjType == NPC
2. NPC의 currentQuest가 null이 아닐 것 
3. NPC의 currentQuest가 수락 가능한 상태 (퀘스트 수락을 위한 대화)
4. NPC의 currentQuest가 완료 가능한 상태 (퀘스트 완료를 위한 대화)

위 조건을 OnInterection()에서 작성해준다.  

```c#
private void OnInterection()
{
    //Debug.DrawRay(transform.position + Vector3.up, transform.forward, Color.blue, 1f);
    if (Physics.Raycast(transform.position + Vector3.up, transform.forward, out _hit, 1f, _mask))
    {
        _canInteract = true;

        // hit.Layer가 NPC 일때 (다른 타입이 추가 될경우 else if또는 switch로 추가)
        if (_hit.collider.gameObject.layer == _npc)
        {
            _interObjType = InteractionObjectType.NPC;
        }

        // 상호작용 UI 표시
        if (!UIManager._Instance._InterUI.gameObject.activeSelf)
        {
            if (_interObjType == InteractionObjectType.NPC && 
                !UIManager._Instance._DialogUI.gameObject.activeSelf &&
                _hit.collider.GetComponent<QuestGiver>()._CurrentQuest != null &&
                (_hit.collider.GetComponent<QuestGiver>()._CurrentQuest._questState == QuestState.Avaliable ||
                _hit.collider.GetComponent<QuestGiver>()._CurrentQuest._questState == QuestState.Completed))
            { 
                UIManager._Instance._InterUI.OnInterText();
            }
        }
        UIManager._Instance._InterUI.SetText(_interObjType);
    }
}
```

이제 NPC 앞으로 가면 UI가 나타날것이다. 하지만 이렇게만하고 끝내면 영원히 꺼지지 않는다.  
이것은 Update 문에서 돌기 때문에 On/Off되는 조건을 걸어주자.  

```c#
[SerializeField] private bool _canInteract;

private void OnInterection()
{
    //Debug.DrawRay(transform.position + Vector3.up, transform.forward, Color.blue, 1f);
    if (Physics.Raycast(transform.position + Vector3.up, transform.forward, out _hit, 1f, _mask))
    {
        _canInteract = true;

        // hit.Layer가 NPC 일때 (다른 타입이 추가 될경우 else if또는 switch로 추가)
        if (_hit.collider.gameObject.layer == _npc)
        {
            _interObjType = InteractionObjectType.NPC;
        }

        // 상호작용 UI 표시
        if (!UIManager._Instance._InterUI.gameObject.activeSelf)
        {
            if (_interObjType == InteractionObjectType.NPC && 
                !UIManager._Instance._DialogUI.gameObject.activeSelf &&
                _hit.collider.GetComponent<QuestGiver>()._CurrentQuest != null &&
                (_hit.collider.GetComponent<QuestGiver>()._CurrentQuest._questState == QuestState.Avaliable ||
                _hit.collider.GetComponent<QuestGiver>()._CurrentQuest._questState == QuestState.Completed))
            { 
                UIManager._Instance._InterUI.OnInterText();
            }
        }
        UIManager._Instance._InterUI.SetText(_interObjType);
    }
    else
    {
        if (_canInteract) { _canInteract = false; }

        if (UIManager._Instance._InterUI.gameObject.activeSelf)
        {
            UIManager._Instance._InterUI.OffInterText();
        }
    }
}
```
bool 타입 _canInteract를 선언하여 On/Off하는 제동을 걸어줬다.  
이제 UI가 켜져있는데 켜지고 꺼져있는데 꺼지는 반복 호출을 하지 않을 것이다.  

자, 그럼 이 canInteract를 이용하여 실제 상호작용하는 메서드를 만들어보자.  
확장성을 고려해 switch문을 사용한다.  

```c#
// 오브젝트와 상호작용
private void Update()
{
    OnInterection();
    InteractObject();
}

private void InteractObject()
{
    if (Input.GetKeyDown(KeyCode.F) && _canInteract)
    {
        switch (_interObjType)
        {
            case InteractionObjectType.NPC:        
                // 대화시작
                break;
            case InteractionObjectType.Item:
                // 실행
                break;
            default:
                break;
        }
    }
}
```

![InteractionUI](https://user-images.githubusercontent.com/97664446/193472277-02de905b-cce3-4809-b07a-bec3fac55a83.gif)

다음은 DialogUI를 할 차례다. 다음편에서 계속~~