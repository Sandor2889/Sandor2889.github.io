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

private float _scaleFactor = 0.334f;
private Coroutine _coroutinMap;

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

다음엔 퀘스트의 위치, NPC의 위치를 추가해 보겠다.