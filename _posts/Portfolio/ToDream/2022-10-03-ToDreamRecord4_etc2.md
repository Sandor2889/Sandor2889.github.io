---
layout: single 
title: "To-dream : 기록 제 4.5장 : NPC 상호작용 부터 퀘스트 확인까지 2편"
categories: ToDream
tag: Portfolio
---

이전편에서 상호작용하는 것을 만들어 보았다.  
이제 실질적인 대화 부분을 만들어 보자.  

# DialogUI

대화 창에는 NPC의 이름, 대화 내용, 다음(Next) 텍스트가 있다.  
next를 하면 다음 대화로 넘어가야하기 때문에 idx를 선언하여 순차적으로 진행하게 한다.  
대화 내용이 담긴 List는 Quest 부분에서 선언되어 있다.  
그리고 대화가 시작되면 상호작용 텍스트는 꺼주도록 한다.

대화가 진행될때 현재 idx의 상태에 따라 달리 구성되어야한다.  
Quest 부분에는 또한 OpenQuestIdx라는 변수가 선언되어 있는데 이 변수는 퀘스트 수락 창이 나타나는 타이밍을 말한다. 만약 1이라면 idx가 0과 1일 때의 대화를 진행하고 퀘스트 수락 창이 나타날 것이다. 
1. idx == OpenQuestIdx + 1 : 퀘스트 수락 창 오픈 (QuestUI)
2. idx >= 대화 수     : 대화 종료
3. idx <  대화 수     : 다음 대화로 넘어가기


```c#
public class DialogUI : MonoBehaviour
{
    [SerializeField] private Text _dialog;                   // NPC 대화 텍스트
    [SerializeField] private Text _npcName;                  // NPC 이름 텍스트
    [SerializeField] private Text _nextText;                 // 다음 텍스트

    [HideInInspector] public int _dialogIdx;

    public void OpenDialog()
    {
        gameObject.SetActive(true);
        UIManager._Instance._InterUI.OffInterText();
        StartCoroutine(C_CloseDialog());
        UpdateDialog();
    }

    public void CloseDialog()
    {
        _dialogIdx = 0;
        gameObject.SetActive(false);
    }

    public void UpdateDialog()
    {
        UIManager uiMgr = UIManager._Instance;
        QuestGiver giver = UIManager._Instance._QuestUI._questGiver;

        // 퀘스트 창 Open 시점 (해당 Idx의 Text가 출력이 되야 하기 때문에 +1)
        if (_dialogIdx == giver._CurrentQuest._openQuestIdx + 1)
        {
            // 퀘스트 창 열기
        }
        // 퀘스트 수락 후 작별 인사
        else if (_dialogIdx >= giver._CurrentQuest._talk.Count)
        {
            CloseDialog();
        }
        // 퀘스트 수락 전 대화 (dialogIdx == 0 ~ openQuestIdx - 1)
        else
        {
            string str = giver._CurrentQuest._talk[_dialogIdx];
            SetText(giver._npcName, str);
            _dialogIdx++;
        }
    }

    public void SetText(NPCName npc, string msg)
    {
        _npcName.text = npc.ToString();
    }

    public IEnumerator C_CloseDialog()
    {
        while (gameObject.activeSelf)
        {
            if (Input.GetKeyDown(KeyCode.Escape) && !UIManager._Instance._QuestUI.gameObject.activeSelf)
            {
                CloseDialog();
            }
            yield return null;
        }
    }
}
```

다음 대화로 넘어가기 위해선 대화창 판넬 자체가 버튼으로 이루어져 있어  
아무대나 클릭하면 버튼 이벤트로 UpdateDialog를 호출 시킨다.  
만약 퀘스트 수락하기전 도중에 종료하게되면 dialogIdx를 초기화 시킨다.  

이대로하면 밋밋하기 때문에 Next 텍스트는 반짝반짝 거리게하고  
대화 텍스트는 한글자씩 타이핑하듯이 효과를 줘보았다.

```c#
[SerializeField] private float _typingSpeed = 0.1f;      // 대화 텍스트 출력 속도
[SerializeField] private float _effectSpeed = 0.5f;      // 다음 텍스트 효과 속도 

private Coroutine _preCort;                              // 이전 출력중인 텍스트의 코루틴

public void SetText(NPCName npc, string msg)
{
    _npcName.text = npc.ToString();
    if (_preCort != null) { StopCoroutine(_preCort); }  // 첫 실행 예외처리
    _preCort = StartCoroutine(C_TypingEffect(msg));
}

private IEnumerator C_TypingEffect(string msg)
{
    _dialog.text = "";
    foreach (char letter in msg.ToCharArray())
    {
        _dialog.text += letter;
        yield return new WaitForSeconds(_typingSpeed);
    }
}

private IEnumerator C_NextTextEffect()
{
    while(gameObject.activeSelf)
    {
        _nextText.text = "Next";
        yield return new WaitForSeconds(_effectSpeed);

        _nextText.text = " ";
        yield return new WaitForSeconds(_effectSpeed);
    }
}
```

다만 타이핑효과의 경우 한 대화문이 끝나기전에 유저가 다음 대화로 넘길 수 있기 때문에 따로 종료시켜줄 필요가 있었다.  

이제 퀘스트 창을 열어보자.  

# QuestUI

퀘스트 창은 NPC의 currentQuest를 참조하여 해당 정보들을 가져와 내용을 채워주기만하면 된다.  
수락 버튼과 거절 버튼에 대한 기능도 만들어 주면 된다.  
그런데 QuestGiver에 참조하려면 상호작용할때 어떤 NPC인지 정보를 넘겨줄 필요가 있다.  
잠시 ObjectInteraction 스크립트로 돌아가보자.  
OnInterection()에서 상호작용 UI를 켜주는 부분에서 정보를 넘겨준다.  
그리고 else 부분에서는 null로 초기화 시켜주도록 하자.  

```c#
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

        // ----- 코드 추가 ------
        UIManager._Instance._QuestUI._questGiver = _hit.collider.GetComponent<QuestGiver>();
    }
}
```

다시 돌아와서 QuestUI를 채워준다.  
```c#
[SerializeField] private Text _title;
[SerializeField] private Text _description;
[SerializeField] private Text _goal;
[SerializeField] private Text _gold;
[SerializeField] private Text _itemName;
[SerializeField] private Image _itemImage;

[HideInInspector] public QuestGiver _questGiver;

public void OpenQuest()
{
    Quest quest = UIManager._Instance._QuestUI._questGiver._CurrentQuest;

    if (quest._questState != QuestState.Avaliable) { return; }  // 예외 처리

    SetText(quest);              // 퀘스트 내용 업데이트
    gameObject.SetActive(true);
}

public void CloseQuest()
{
    gameObject.SetActive(false);
}

public void AcceptQuest()
{
    UIManager uiMgr =UIManager._Instance;
    QuestGiver giver = uiMgr._QuestUI._questGiver;

    giver._CurrentQuest.Accepted(); 
    CloseQuest();

    // 현재 진행된 대화 인덱스가 퀘스트의 총량 보다 작으면 동작 (퀘스트 수락 후 대화로 넘어감)
    // 퀘스트 수락 후 대화가 없다면 대화 종료.
    if (uiMgr._DialogUI._dialogIdx < giver._CurrentQuest._talk.Count)
    {
        uiMgr._DialogUI.SetText(giver._npcName, giver._CurrentQuest._talk[uiMgr._DialogUI._dialogIdx]);
        uiMgr._DialogUI._dialogIdx++;
    }
    else
    {
        uiMgr._DialogUI.CloseDialog();
    }
}

public void RefuseQuest()
{
    CloseQuest();
    UIManager._Instance._DialogUI.CloseDialog();
}

private void ActivateRewardItem(bool isReward)
{
    _itemImage.gameObject.SetActive(isReward);
    _itemName.gameObject.SetActive(isReward);
}
```

수락과 거절은 버튼 이벤트로 호출된다.  
수락하였을 때 남아있는 대화가 있다면 대화 내용을 갱신한다.  
없다면 그대로 종료된다.  

그리고 만약 보상 아이템이 없다면 이미지와 텍스트를 꺼주는 메서드를 만들어 주었다.  

![QuestUI](https://user-images.githubusercontent.com/97664446/193474173-a9351cf4-864e-4f62-a317-d1dd9cc6debe.gif)  

대화 시작부터 퀘스트 수락하는 과정이다.  

