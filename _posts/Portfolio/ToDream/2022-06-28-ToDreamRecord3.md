---
layout: single 
title: "To-dream : 기록 제 3장 (Remake) : Buoyancy"
categories: ToDream
tag: Portfolio
---

다시 시작하기로 하였다. <br>
기획도 다시하고 지난번엔 생각없이 구현을 목적으로 코드를 짰다면   
이번엔 디자인 패턴도 고려하며 설계도하고 안 해본 기능, 기법들도 사용해보고자 한다.  
마지막엔 최적화도 해볼 것이다.  
  
이번에도 마찬가지로 탈것을 이용한다.  
좀더 게임성을 부여하기 위해 퀘스트 시스템을 도입하기로 했다.  
배달과 목표지점 도달 이렇게 있을 예정이다.  

이전처럼 맵 작업에 너무 시간을 쏟지 않기로 하였고, 
게임의 핵심인 물리구현들 먼저 하기로 했다.  
그러므로 나는 현재 부력, 보트의 구현을 진행하고 있다.

***

파도를 구현하기 위한 대표적인 알고리즘으로 `Gerstner Wave`가 있다.  
여러 자료를 참고하여 만들어 보며 셰이더를 처음 다루게 되었고  
Mesh와 MeshFilter 그리고 vertices, triangle, uv 각각에대해 조금 알게 되었다.  

sea of thieves 라는 게임이 있다. 물 그래픽이 굉장히 좋고 배를 조종하면 바다위에 있다는 느낌이 난다.  
퀄은 떨어지더라도 저런 조작감이 있었으면 해서 이를 목표로 만들기 시작했다.
그러나 Unity URP의 셰이더 그래프를 사용해야 했고 그렇게되면 게임이 목적과 다르게 무거워진다.  
그래서 다른 자료들을 계속해서 찾아보다가 합의를 보게되었다.  

'잔잔하지만 어느 정도의 파형이 보이는 바다에서 부력을 구현하고 조금이라도 보트타는 느낌이 들도록'  
이정도는 할만하다. 부력자체는 이미 구현이 되었고, 퀄을 높이기위한 텍스쳐를 구할 필요가 있어졌다.
그리고 맵에 적용시키전 오클루전 컬링이 호환되도록 신경써야 한다.

<p align= "center"> 
<img src="https://user-images.githubusercontent.com/97664446/176895372-1e03c44a-abd0-4ff9-9321-7939754f61c6.gif" alt="WaterFail"> 
</p>

현재 부력 관련으로 테스트 일주일째.  
파도의 이음새들이 보인다.  
유니티에서 제공해주는 BoatAttack에서 활용해야 겠다.  
하지만 URP를 기반으로 만들어져있기때문에 우리 프로젝트에 맞게 변형시켜야 한다.  
어려운 싸움이 될것으로 보인다...

1. Skybox를 푸르게 변경
2. 카메라를 따라다니는 water mesh 적용 (테셀레이션 효과)
3. job system과 셰이더, hlsl  
에 대해 알아야 한다 !!!


***

WaterSystem을 만들기 시작한지 일주일이 다 되어간다.  
여전히 만들지 못하고 있다.  
Shader에대한 튜토리얼 영상도 보고...  
boatAttack의 Water 코드를 뜯어보고...  

그렇게 시도를 하였지만 아직 문제를 찾지 못하고 있다.  

`RenderPipelineManager.beginCameraRendering`  
문제의 요인인 코드이다.  
URP에서만 호환되며 Built-in에서 사용하려면 `Camera.OnPreRender`를 사용해야 한다고 한다.  

이것은 매프레임 카메라 렌더링하기전에 호출 한다고 한다.
델리게이트로써 원하는 기능을 추가해주면 update()하듯이 호출한다.

URP 프로젝트를 따로 만들어 위 코드로 Water Mesh를 만들어 보았다.

<p align= "center"> 
<img src="https://user-images.githubusercontent.com/97664446/177155687-94a1be2f-5364-4a1c-a8a0-0583003f2e83.gif" alt="WaterMesh"> 
</p>

씬뷰 카메라와, 메인 카메라 둘의 위치에 Water Mesh를 그려주는 것을 볼 수 있다.
이것이 첫걸음이 될 요소이다.  

그러나 !  
Built-in에서 Camera.OnpreRender로 바꾸고 해보면 위와같이 Mesh가 보이질 않는다.  
Debug를 해보면 오류없이 작동은 하고있었다.  
원인을 찾기 위해 질문글도 올려보고 하였지만 도저히 찾을 수가 없다.  

며칠이 지난 오늘 Update에서 돌려보았다.  
본래는 씬cam과 메인cam을 왔다갔다하면서 렌더링해주지만 update에서 하게되면  
코드가 다소 복잡해지기 때문에 메인cam에서만 그려지게 하였다.  
그래서 그런지 vertices는 보이지 않지만 화면에 무언가가 깔려 있고 메인cam을 따라다니는 것을 볼 수 있었다.  

이제 Shader를 작성해 이 깔려있는 무언가가 제대로 되어있는지 확인해보고자 한다.

***

<p align= "center"> 
<img src="https://user-images.githubusercontent.com/97664446/177311576-9f1c4816-b34e-4876-8bfc-4c081a96323c.PNG" alt="URPWater">  
Built-in


<img src="https://user-images.githubusercontent.com/97664446/177311583-91346a64-4959-48de-9c6e-c12bf1b8ed66.PNG" alt="BuiltInWater">  
URP 
</p>

드디어 Built-in에 Water를 깔았다.  
URP 버전의 Water와 비교하면 다소 투박하다.  
이제 셰이더를 어떻게든 만져줘야 할것 같다.