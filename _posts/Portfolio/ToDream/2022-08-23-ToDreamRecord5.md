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
2. Dictionary<int, GameObject>로 중복 배치 하지 않도록 처음 등록시 추가 시킨다.  
3. 각 탈것의 타입별로 마지막에 등록된 프리팹을 저장한다. (GameObject[] currentVehicles)  
4. 두번째 등록시 currentVehicles에 값을 초기화 시켜주면 된다.

```c#
// 등록 버튼 누를 시
public void OnRegister()
{
    GameObject obj = GameManager.GetDicValue(_itemKey)._obj;
    int category = (int)GameManager.GetDicValue(_itemKey)._category;

    if (!_registeredObjs.ContainsKey(_itemKey))     // 첫 등록 시
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
    
    OnBack();
}
```

![VehicleRegistration](https://user-images.githubusercontent.com/97664446/186103458-1c0e542a-27b8-451f-891e-e0d399672b6f.gif)  

레이싱카를 등록하고 Car에 등록된 탈것을 타고  
비행기를 등록하고 Air에 등록된 탈것을 타보았다.