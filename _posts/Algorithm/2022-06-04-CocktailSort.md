---
layout: single
title: "칵테일 정렬"
categories: Algorithm
tag: [Algorithm, Sort]
toc: true
toc_sticky: true
---


# Description
칵테일 정렬은 버블 정렬에서 한단계 개선된 정렬이다. <br>
양방향버블정렬 또는 셰이커정렬이라고도 불리운다. <br>
정렬 방식은 버블 정렬과 같되 왼쪽과 오른쪽을 왕복한다. <br>
`시간 복잡도 = O(N^2)` 이다


우선, 버블정렬 처럼 왼쪽에서 시작한다. (버블정렬과 정렬방식이 같음으로 자세한 설명은 생략한다)
![Desc1](https://user-images.githubusercontent.com/97664446/172000462-41928272-4158-482f-a371-9fedc4970ee6.PNG)
<br>
![Desc2](https://user-images.githubusercontent.com/97664446/172000465-b79f19c7-1434-4ef5-b82a-982c65414fb4.PNG)
<br>
![Desc3](https://user-images.githubusercontent.com/97664446/172000466-1fb71982-9d58-4615-8fb4-050432cef14f.PNG)
<br>
![Desc4](https://user-images.githubusercontent.com/97664446/172000467-f891a397-c0d2-40b9-9fb2-2db215bbfc62.PNG)
<br>
![Desc5](https://user-images.githubusercontent.com/97664446/172000469-814a9ee3-caa9-49f5-89ed-015a61de5970.PNG)
<br>
![Desc6](https://user-images.githubusercontent.com/97664446/172000470-e1043f06-087c-487b-bc80-101f81dbd461.PNG)
<br>
![Desc7](https://user-images.githubusercontent.com/97664446/172000471-354158c7-8c67-4455-b870-ef11a50f114d.PNG)
<br>
![Desc8](https://user-images.githubusercontent.com/97664446/172000472-f3a665c4-3647-4431-a339-35046c9501c9.PNG)

여기까지는 버블과 같다, 우측에서부터 정렬 하되 확정된 숫자 9는 제외하고 시작한다. <br>
우측에서 시작 할땐 작은 수가 왼쪽으로 가도록 정렬하면 된다. <br>

![Desc9](https://user-images.githubusercontent.com/97664446/172000912-a4dc2561-f651-4e6d-8a0b-d3b9728c0c66.PNG)
6과 1을 비교한다, 더 작은 수인 1이 왼쪽에 있기때문에 자리 바꿈은 없다. <br>

![Desc10](https://user-images.githubusercontent.com/97664446/172000914-f0a64a25-86d7-4b27-a4b9-2085e476bfcc.PNG)
1과 3을 비교한다, 작은 수인 1을 왼쪽으로 가도록 3과 자리를 바꾼다 <br>

![Desc11](https://user-images.githubusercontent.com/97664446/172000916-119bb6e6-0b63-4e4a-8917-84d41d0cca4f.PNG)
1과 4를 비교한다, 1과 4의 자리를 바꾼다. <br> 

![Desc12](https://user-images.githubusercontent.com/97664446/172000917-baf61d76-e726-41fd-b906-f4b232ddcf32.PNG)
8과 1의 자리를 바꾼다. <br>

![Desc13](https://user-images.githubusercontent.com/97664446/172000918-9a4bfd44-0729-47d6-967c-eb00ed20a71e.PNG)
7과 1의 자리를 바꾼다. <br>

![Desc14](https://user-images.githubusercontent.com/97664446/172000920-aedd1591-91a0-4b2e-8463-73f6f6867097.PNG)
5와 1의 자리를 바꾼다. <br>

![Desc15](https://user-images.githubusercontent.com/97664446/172000921-f347bf87-f83f-460d-9f1c-c9be9a56f801.PNG)
2와 1의 자리를 바꾼다. <br

![Desc16](https://user-images.githubusercontent.com/97664446/172000924-467ee178-2f5d-4c6d-9ddd-27ae5f8344ba.PNG)
이제 1의 자리가 확정되었다. <br>
또 다시 좌측에서 시작하여 정렬하고 끝나면 우측에서 정렬을 시작하고 돌아와 숫자가 각각 하나씩 <br>
끝에서부터 확정되어가는 방식이다. <br>

*** 

# Cord
```c++
#include <iostream>

#define LENGTH 9 // 배열의 길이

using namespace std;

int main()
{
    int dataArr[LENGTH] = { 2, 5, 7, 8, 4, 9, 3, 1, 6 };
    
    int evenCount = 0;  // 짝수 실행 수
    int oddCount = 0;   // 홀수 실행 수
    int temp;
    bool flag = true;   // 정렬 중단 스위치

    // Cocktail sort 실행
    for (int idx = 0; idx < LENGTH; idx++)
    {
        flag = true;
        if (idx % 2 == 0) // 짝수 일때
        {
            for (int even = 0 + oddCount; even < LENGTH - 1 - evenCount; even++)
            {
                // 왼쪽에 있는 수가 오른쪽 보다 크면 자리 바꾸기 (큰 수를 오른쪽으로)
                if (dataArr[even] > dataArr[even + 1])
                {
                    temp = dataArr[even];
                    dataArr[even] = dataArr[even + 1];
                    dataArr[even + 1] = temp;
                    flag = false;
                }
            }
            evenCount++;
        }
        else // 홀수 일때
        {
            for (int odd = LENGTH - 1 - evenCount; odd > 0 + oddCount; odd--)
            {
                // 오른쪽에있는 수가 왼쪽 수보다 작으면 자리 바꾸기 (작은 수를 왼쪽으로)
                if (dataArr[odd] < dataArr[odd - 1])
                {
                    int temp = dataArr[odd - 1];
                    dataArr[odd - 1] = dataArr[odd];
                    dataArr[odd] = temp;
                    flag = false;
                }
            }
            oddCount++;
        }

        // 더 이상 정렬 할 숫자가 없으면 끝내기
        if (flag) { break; }
    }

    // 결과 출력
    string result;
    for (int idx = 0; idx < 9; idx++)
    {
        if (idx == 8)
        {
            result += dataArr[idx] + '0';
        }
        else
        {
            result += dataArr[idx] + '0';
            result += ", ";
        }
    }
    cout << "data = {" << result << " }";
    cout << "\n\n\n\n\n\n\n";
    return 0;
}
```

***

# Result

![Result](https://user-images.githubusercontent.com/97664446/172001697-90b5ea27-f559-4033-a23a-22b497f8d8eb.PNG)

