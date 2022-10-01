---
layout: single 
title: "To-dream : 기록 제 5장 : Item & Vehicle registration"
categories: ToDream
tag: Portfolio
---

이번에 작성할 것은 ItemDataBase에 아이템을 등록하고  
보상으로 받은 탈것 아이템을 인벤토리에서 등록하고 타는 것이다.

# Item Data Base
게임 내 Item Data들을 관리한다.  
Dictionary<int, Item> 타입으로 int key값을 이용해 퀘스트 보상으로 받을 Item을 찾아 받는다.  
Dictionary는 인스펙터에서 볼 수가 없어 `ISerializationCallbackReceiver`를 통해 직렬화 시켰고  
커스텀 에디터로 내가 원하는 대로 꾸몄다.  

![ItemDataBase](https://user-images.githubusercontent.com/97664446/186098695-a98e706c-8600-48fb-b9d0-11c5b682506f.gif)  

# Inventory
아이템의 타입별로 카테고리를 분류하여 나타내기로 하였다.  
현재 아이템 타입은 Car, Air, Boat, Consumable이 있고  
Consumable만 예외로 소모품 슬롯에 자동으로 장착된다.

인벤토리의 각각의 슬롯이 어떤 아이템을 디스플레이 해야하는지 알아야 하기 때문에
아이템 습득시 List<Item> items 에 저장시켰고  
각 슬롯이 Items의 item 카테고리를 탐색해 슬롯에 그려주었다.  

```c#
    public void SortByCategory(Category category)
    {
        ClearSlotData();
        PressedCategory(category);

        List<Item> copyList = _items.ToList<Item>();   // Item List 복제하여 활용

        for (int i = 0; i < _slots.Length; i++)
        {
            Item item = copyList.Find(x => x._category == _category);

            if (item == null) { return; }

            _slots[i].SetItem(item._key);
            copyList.Remove(item);
        }
    }

```

![Inventory](https://user-images.githubusercontent.com/97664446/186101465-99c9dd8d-0556-4987-a8bd-4eb5c2a59072.gif)  

아직 Item Sprite 들을 제대로 넣어주지 않아 임시로 넣은 이미지이다.  
아무튼 탈것들이다. ㅋㅋ  
퀘스트 보상으로 소모품, 레이싱카, 트럭, 비행기를 받았고  
소모품은 왼하단 슬롯에, 나머지 탈것들은 인벤토리 카테고리 분류에따라 나뉜 것을 볼 수 있다.  

## Vehicle registration
C키를 눌러 현재 등록된 타입의 탈것을 탈 수 있다.  
아직 보트는 미완성으로 자동차와 비행기를 타보자. 

1. 탈것을 처음 등록 할땐 씬에 해당 프리팹을 불러와 배치 시킨다. 
2. Dictionary<int, GameObject>을 선언하여 아이템 중복 배치 하지 않도록 처음 등록시 추가 시킨다.  
3. 각 탈것의 타입별로 마지막에 등록된 프리팹을 저장한다. (GameObject[] currentVehicles)  
4. 두번째 등록시 currentVehicles[] 해당 인덱스의 값을 초기화 시켜주면 된다.

```c#
private Dictionary<int, GameObject> _registeredObjs = new Dictionary<int, GameObject>(); // 씬에 배치된 아이템

public GameObject _buttonPanel;
public int _itemKey;    // 슬롯 클릭시 아이템 key 값 저장되어옴

// 등록 버튼 누를 시
public void OnRegister()
{
    GameObject obj = GameManager.GetDicValue(_itemKey)._obj;
    int category = (int)GameManager.GetDicValue(_itemKey)._category;

    if (!_registeredObjs.ContainsKey(_itemKey))     // 첫 등록 시
    {
        obj = Instantiate(obj);  // 씬에 오브젝트 생성
        VehicleWheelController._currentVehicles[category + 1] = obj;
        _registeredObjs.Add(_itemKey, obj);
        obj.SetActive(false);
    }
    else
    {
        _registeredObjs.TryGetValue(_itemKey, out obj);
        VehicleWheelController._currentVehicles[category + 1] = obj;
    }
    
    OnBack();
}
```

_itemkey의 경우 Slot class에서 버튼 이벤트로 슬롯 클릭시 호출된다.  
```c#
public void OnRegistrationButton()
{
    if (_itemKey == -1) { return; }

    _registration._itemKey = _itemKey;
    _registration._buttonPanel.transform.position = gameObject.transform.position + _offset;
    _registration.gameObject.SetActive(true);
}
```

![VehicleRegistration](https://user-images.githubusercontent.com/97664446/186103458-1c0e542a-27b8-451f-891e-e0d399672b6f.gif)  

레이싱카를 등록하고 Car에 등록된 탈것을 타고  
비행기를 등록하고 Air에 등록된 탈것을 타보았다.

***

동료분과 코드 리뷰한 이후 추가 요청으로 각  
아이템들의 On/Off 상태를 나타내는 것이 필요하다는 의견이 나왔다.  
그리고 버튼 기능 중 등록해제도 추가하기로 하였다.  
이 기능들을 추가시켜 보도록 하자.  


### 등록해제

우선 등록 해제 버튼을 추가해보자  
![Buttons](https://user-images.githubusercontent.com/97664446/193415155-001c326b-fbb2-4c5b-8f0f-ea979448a931.PNG)
![Release](https://user-images.githubusercontent.com/97664446/193415150-c5f13244-2fa1-44dd-8607-d0ba69626d51.PNG)

그리고 Realse 메서드를 만들어 버튼 이벤트에 추가시키면 된다.  
- currentVehicles[]의 인덱스 값을 초기화 시킨다. idx = (아이템의 카테고리 + 1)   
```c#
// 등록 해제
public void Release()
{
    int category = (int)GameManager.GetDicValue(_itemKey)._category;

    VehicleWheelController._currentVehicles[category + 1] = null;   // 해당 카테고리 등록 초기화
    OnBack();
}
```

### On/Off 텍스트  
우선 표시될 텍스트를 각각의 slot에 추가한다.  
![text](https://user-images.githubusercontent.com/97664446/193415151-f6c695d0-afcc-4b84-8da7-b3c2cab662e8.PNG)

각각의 탈 것 아이템이 등록 상태를 확인하기 위해 ResgistrationState class를 만들고 붙여주었다.  

```c#
public class RegistrationState : MonoBehaviour
{
    public bool _isRegistered; // 등록 여부
}
```

그러면 이 아이템이 등록이 되면 true / 해제되면 false 시킬 것이다.  
위에서 다뤘던 OnRegister()와 Release()에서 살펴보자.

```c#
public void OnRegister()
{
    GameObject obj = GameManager.GetDicValue(_itemKey)._obj;
    int category = (int)GameManager.GetDicValue(_itemKey)._category;

    GameObject preObj = VehicleWheelController._currentVehicles[category + 1];  // 이전에 등록한 탈 것

    if (!_registeredObjs.ContainsKey(_itemKey)) // 첫 등록 시
    {
        obj = Instantiate(obj);
        VehicleWheelController._currentVehicles[category + 1] = obj;
        _registeredObjs.Add(_itemKey, obj);
        obj.SetActive(false);
    }
    else
    {
        _registeredObjs.TryGetValue(_itemKey, out obj);
        VehicleWheelController._currentVehicles[category + 1] = obj;
    }

    // ------ 추가 된 코드 --------
    // slot의 ON/OFF Text - 현재 등록된 카테고리의 탈것과 다른것을 등록할 경우
    if (preObj != null && preObj != obj)
    {
        preObj.GetComponent<RegistrationState>()._isRegistered = false;
    }
    obj.GetComponent<RegistrationState>()._isRegistered = true;
    OnBack();
}
```

이전에 등록한 아이템을 해제시켜줘야 하기 때문에 preObj를 추가시켜주었다.  
그리고 이전 아이템과 이번에 등록한 아이템을 비교하여 처리해주면 된다.  

Release()도 살펴보자.
```c#
public void Release()
{
    Item item = GameManager.GetDicValue(_itemKey);
    int category = (int)item._category;

    GetObjDicValue().GetComponent<RegistrationState>()._isRegistered = false;
    VehicleWheelController._currentVehicles[category + 1] = null;
    OnBack();
}

public GameObject GetObjDicValue()
{
    GameObject obj;
    _registeredObjs.TryGetValue(_itemKey, out obj);
    return obj;
}
```
아이템의 등록 상태를 바꿔줘야 하기 때문에 이와 같이 코드를 다시 정리하였다.  

이제 각 슬롯의 텍스트를 바꿔주는 코드를 짜야한다.  
Slot class에서 다음과 같은 메서드를 만든다.  

```c#
public void SetRegisteredText(GameObject itemObj)
{
    if (_itemKey == -1) // -1: 빈 슬롯
    {
        _stateText.gameObject.SetActive(false);
    }
    else // null, 등록 상태, 슬롯의 key와 등록된 카테고리의 아이템 동일 여부 비교
    {
        if (itemObj == null || 
            !itemObj.GetComponent<RegistrationState>()._isRegistered || 
            _itemKey != itemObj.GetComponent<RegistrationState>()._itemKey)
        {
            _stateText.text = "OFF";
        }
        else if (itemObj.GetComponent<RegistrationState>()._isRegistered &&
            _itemKey == itemObj.GetComponent<RegistrationState>()._itemKey)
        {
            _stateText.text = "ON";
        }

        _stateText.gameObject.SetActive(true);
    }
}
```

인벤토리에서 모든 슬롯에 대해 이 메서드를 호출 시켜준다. 왜냐하면 a 아이템을 등록한 상태에서 b 아이템을 등록하게되면  
a는 Off / b는 On으로 바뀌어야 하므로 한 대상의 슬롯이 아닌 전체를 다시 설정해주는게 더 나아보였다.

```c#
// 슬롯의 탈것 등록 상태
public void UpdateRegisteredSlot()
{
    for (int i = 0; i < _slots.Length; i++)
    {
        _slots[i].SetRegisteredText(VehicleWheelController._currentVehicles[(int)_category + 1]);
    }
}
```

그런데 현재 위 코드로는 아무 상관 없는 아이템을 등록 해제해도 모든 아이템이 해제가 되어버린다.  
그래서 클릭한 슬롯의 아이템의 등록 여부에따라 버튼 interactable을 끄고 켜주기로 하였다.  
(더 깔끔하고 좋은 방법을 찾아보려 하였으나... 실패닷;;)  


슬롯을 클릭하면 등록패널이 호출되는 코드이다. (패널만 끄고키는 기능이라 추가 안 하려고 했는데...) 
```c#
public void OnRegistrationButton()
{
    if (_itemKey == -1) { return; }

    _registration._itemKey = _itemKey;
    _registration._buttonPanel.transform.position = gameObject.transform.position + _offset;


    // -------코드 추가 -------
    // 탈 것이 미등록 상태면 Release 버튼 활성화
    GameObject obj;
    _registration._registeredObjs.TryGetValue(_itemKey, out obj);
    if (obj != null && obj.GetComponent<RegistrationState>()._isRegistered)
    {
        _registration.SetReleaseButtonInter(true);
    }
    // -------------------------
    _registration.gameObject.SetActive(true);
}
```

원래는 아이템 key 전달, 버튼 패널의 위치 잡기, 활성화하기 뿐이였는데  
탈 것의 등록상태를 확인하는 조건문이 추가 되어 이렇게 소개하기로 하였다.  
SetReleaseButtonInter는 이름 그대로 등록해제 버튼의 Interactable를 조절하는 역할을 한다.  

자 그러면 VehicleRegistration class로 돌아와 등록/해제 할때 
각 슬롯의 텍스트를 업데이트 시켜주는 UpdateRegisteredSlot()을 각각 추가시켜주면 끝이다.  

# Result
![AddRelease](https://user-images.githubusercontent.com/97664446/193415152-114bcfa1-c49d-49e3-8fd5-9792c3892274.gif)

등록 텍스트만 추가 시켰는데도 엄청나게 복잡해졌다...  
더 좋은방법을 알고 싶다...  