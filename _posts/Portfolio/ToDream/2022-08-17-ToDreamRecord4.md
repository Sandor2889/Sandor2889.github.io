---
layout: single 
title: "To-dream : 기록 제 4장 : QuestSystem"
categories: ToDream
tag: Portfolio
---

바다 표현하는 것은 뒤로 미루기로 하여 나는 우선 QuestSystem을 만들어 보기로 하였다.  

# Quest
그렇다면 우선 Quest에서 필요한것들을 살펴보자.  
기본적으로 필수 요소 4가지가 있다.
- Title
- Description
- Goal
- Reward

대략적으로 Quest의 구조는 이렇다.  
![Quest](https://user-images.githubusercontent.com/97664446/185072117-1b8e13c2-d09b-4f12-ad42-38dcf1814901.PNG)
(UML 작성법을 몰라 흉내만 내보았다.)

## QuestGoal
추가로 하나의 퀘스트에서 목표가 여러가지 일 수 있기 때문에 이와같이 만들었다.
```c++
public enum GoalState
{
    InProgress,     // 진행중
    Complete        // 목표량 달성
}

[System.Serializable]
public class QuestGoal
{
    public GoalState _goalState;                          // 진행 상태
    public string _target;                                // 퀘스트 목표
    public string _subTitle;                              // 서브 타이틀
    public int _currentTargetCount;                       // 현재 수량
    public int _requiredAmount;                           // 요구 수량
    public QuestTargetMarker _targetMarker;               // 퀘스트 목표 지점 마커 활성화

    // 이하 생략...
}
```

각각의 목표마다 타겟, 수량, 진행상태가 있다.

## QuestManager
QuestManager에서 내가 만든, 등록된 Quest 데이터들을 모두 저장한다.  
어떤 Quest가 수락되었고 어떤 Quest가 완료 되었는지 따로 관리하며  
각 퀘스트의 진행 상태를 계속해서 Update 시켜주는 역할을 해준다.  

```c#
    #region Singleton
    private static QuestManager _instance;
    public static QuestManager _Instance
    {
        get
        {
            if (_instance == null)
            {
                _instance = FindObjectOfType<QuestManager>();
                if (_instance == null)
                {
                    _instance = new GameObject("QuestManager").AddComponent<QuestManager>();
                    DontDestroyOnLoad(_instance.gameObject);
                }
            }
            return _instance;
        }
    }
    #endregion

    public List<Quest> _questContainer = new List<Quest>();             // 퀘스트 데이터베이스
    public List<Quest> _acceptedQuests = new List<Quest>();     // 수락한 퀘스트
    public List<Quest> _doneQuests = new List<Quest>();         // 완료된 퀘스트

    private void Awake()
    {
        _instance = this;
        InitCode();
    }

    // 퀘스트 진행률 업데이트
    public void ReceiveReport(string target, int counting)
    {
        for (int i = 0; i < _acceptedQuests.Count; i++)
        {
            if (!IsTarget(_acceptedQuests[i])) { continue; }

            _acceptedQuests[i].ReceiveReport(target, counting);
        }
    }

    // 퀘스트의 Target과 State 조건 확인
    public bool IsTarget(Quest quest)
    {
        if (quest._questState == QuestState.Accepted )
        {
            return true;
        }

        return false;
    }

    // 이하 생략...
```

## Etc..

이외의 것들은 아래와 같은 방식으로 구현하였다.
1. QuestGiver(NPC)들은 QuestManager에서 자신의 Quest를 찾아 List에 저장한다.
2. QuestGiver의 CurrentQuest를 업데이트한다. (순차적인 퀘스트 진행방식이므로 한 NPC당 하나의 퀘스트를 진행할 수 있다.)
3. ObjectInteraction(Player)가 Ray()를 쏴 NPC를 감지하고 CurrentQuest가 활성화 된 NPC와 대화가 가능하다.
5. 대화시 Dialog UI가 열리며 대화가 진행된다.
6. 특정 대화에 도달하면 Quest 수락 창이 열린다. (수락 및 거절)
7. Quest 목록창에서 현재 수락한 Quest를 확인 할 수 있다. (포기하기, 돌아가기)
8. 각 NPC마다 CurrentQuest의 상태에 따라 마커를 띄운다 (수락가능, 진행중, 완료가능)

![Quest](https://user-images.githubusercontent.com/97664446/185073744-205ea34d-9a93-4044-bf26-5b7b4cbf0c98.gif)  
NPC와 대화하고 퀘스트를 받고 퀘스트를 포기했다가 다시 대화하는 모습이다.

## Quest GUIEditor
![QuestGenerator](https://user-images.githubusercontent.com/97664446/185080288-750a0a72-4b8f-473f-88f8-4b6ffc30ce5a.PNG)  

퀘스트를 생성하고 등록, 편집까지 쉽고 자유롭게 하기위해 커스텀 에디터를 활용해보았다.  
이번에 처음 사용해보았고 어떤 기능들이 있는지만 알면 생각보다 적용하기 어렵지 않았다.  
은근 또 이게 꾸미는 재미가 있기도 했다.

![QuestGenerator](https://user-images.githubusercontent.com/97664446/185081215-558a7b3b-4f28-4af2-aa0c-69b47ff88c67.gif)