---
layout: single 
title: "To-dream : 기록 제 7장 : Navigation"
categories: ToDream
tag: Portfolio
---

# Navigation

이번에는 현재 퀘스트 목표가 어딘지 알려주는 네비게이션 기능을 추가 할 것이다.  
캐릭터 상태때나 탈 것을 탄 상태때 모두 기능하여야하기 때문에 메인 카메라 하위로 화살표 모델을 달아 주었다.  

네이게이션의 조건은 다음과 같다.  
- `F`를 누른 상태일때 네비게이션이 켜진다.  
- 네비게이션은 한 번에 하나의 퀘스트와 그 퀘스트중 하나의 목표만 순서대로 가리킨다.  
  즉, 수락한 퀘스트가 두 개가 있다면 가장 먼저 받은 퀘스트에 대해서만 네비게이션이 기능한다.  
  그리고 그 퀘스트의 목표가 여러개라면 가장 먼저 등록되있는 목표부터 가리킨다.  
- 퀘스트 목표지역에 가까워지면 네비게이션 기능을 이용 할 수 없다.


네비게이션을 키고 끄는 메서드를 만든다.  
수락한 퀘스트가 없으면 네비게이션이 켜지면 안된다.  
```c++
public void OnNav()
{
    if (!QuestManager.CheckHasQuest()) { return; }

    gameObject.SetActive(true);
}

public void OffNav()
{
    gameObject.SetActive(false);
}
```

CheckHasQuest()는 수락한 퀘스트가 있는지 검사하고 bool값을 리턴시키는 메서드이다.  
QuestManager class에 위치한다.  
```c++
public static bool CheckHasQuest()
{
    if (_instance._acceptedQuests.Count <= 0)
    {
        return false;
    }
    return true;
}
```

네비게이션이 켜졌을때 목표가 여러개 일 수 있으니 배열 형태로 퀘스트 목표들을 담고 가장 앞부분을 가리키도록 한다.  
만약에 꺼진다면 해당 목표 배열을 초기화 시킨다.  
현재 퀘스트가 가진 목표들을 리스트 형태로 따로 복사한다.  
for문으로 퀘스트 목표가 완료된 것이라면 리스트에서 바로 제외, 아니면 배열에 넣고 제외시키는 방식으로 하였다.  
(킬 때마다 리스트가 생성되고, 이중 for문으로 탐샘해야하는 방식이라 마음에 들지 않지만 현재로선 이렇게 밖에 구현하지 못하겠다.)

```c++
[SerializeField] private float _searchDistance = 10f;
[SerializeField] private GameObject _navArrow;
private Transform[] _targetTr;

public void OnEnable()
{
    Quest currQuest = QuestManager._Instance._acceptedQuests[0];    // 현재 수락한 퀘스트 중 가장 먼저 수락한 퀘스트
    if (currQuest._questState == QuestState.Accepted)   // 퀘스트 진행중인경우 Nav가 목표지점을 가리킨다.
    {
        List<QuestGoal> copyList = currQuest._questGoals.ToList<QuestGoal>();   // 퀘스트의 목표들을 복사
        for (int i = 0; i < _targetTr.Length; i++)
        {
            for (int j = 0; j < copyList.Count; j++)
            {
                // 이미 달성한 목표는 제외시키기 
                if (!copyList[j]._targetMarker.gameObject.activeSelf)
                {
                    copyList.Remove(copyList[j]);
                }

                _targetTr[i] = copyList[j]._targetMarker.transform;
                copyList.Remove(copyList[j]);
                break;
            }
        }
    }
    else if (currQuest._questState == QuestState.Completed) // 퀘스트 완료가능 상태면 NPC를 가리킨다.
    {
        _targetTr[0] = UIManager._Instance._NPCMarkerUI._Npcs[(int)currQuest._npcName].transform;
    }
}

private void OnDisable()
{
    for (int i = 0; i < _targetTr.Length; i++)
    {
        _targetTr[i] = null;
    }
}
```
_targetTr : 퀘스트의 목표는 4개 이상으로 잘 없으니 Awake에서 크기를 3으로 초기화 시켰다.

이제 네비게이션이 켜지고 나서 배열의 0번째 idx에 있는 목표를 계속해서 가리키게 한다.  
Vector 좌표계에서 점과 점의 차는 방향과 거리를 나타낸다. (퀘스트 목표 - 내 위치)  
목표물에 근접했는지 거리를 연산해야한다. sqrMagnitude는 루트 연산을 하지 않기 때문에 속도가 빠르다.  
그래서 비교 대상인 _searchDistance를 제곱하여 비교해야 한다.  
```c++
private void Update()
{
    Vector3 offset = _targetTr[0].position - transform.position;
    float sqrDist = offset.sqrMagnitude;

    // 탐지범위안으로 들어오면 nav 끄기
    if (sqrDist < _searchDistance * _searchDistance) 
    {
        if (_navArrow.GetComponent<MeshRenderer>().enabled)
        {
            _navArrow.GetComponent<MeshRenderer>().enabled = false;
        } 
    }
    else 
    {
        // 렌더러가 꺼져있다면 켜주기
        if (!_navArrow.GetComponent<MeshRenderer>().enabled)
        {
            _navArrow.GetComponent<MeshRenderer>().enabled = true;
        }
        transform.LookAt(_targetTr[0]);
    }
}
```

# Result
![Navigation](https://user-images.githubusercontent.com/97664446/192155897-fc1b4642-364b-4d29-b89d-5919c05ffa77.gif)  

이렇게 `F`를 누르는 동안 네비게이션이 켜지고 목표에 가까워지면 자동으로 꺼지는 것을 볼 수 있다. 
