---
layout: single
title: "선택 정렬"
categories: Algorithm
tag: [Algorithm, Sort]
toc: true
toc_sticky: true
---


# Description
왼쪽에서부터 한 자리씩 정렬해 나간다 <br>
기준이 되는 숫자와 나머지 숫자를 비교하여 작은 수를 왼쪽으로 보내면 된다 (오름차순)<br>
시간 복잡도는 O(N^2) 이다

![Desc1](https://user-images.githubusercontent.com/97664446/170832248-54401f58-438c-4670-a8b8-ed952133031b.PNG) <br>
2와 5중에 2가 더 작은 수이기 때문에 다음으로 넘어간다

***

![Desc2](https://user-images.githubusercontent.com/97664446/170832249-cfe3d363-317d-4550-879e-db0374bf57fe.PNG) <br>
2와 7을 비교 한다, 자리바꿈 없음<br>

***

![Desc3](https://user-images.githubusercontent.com/97664446/170832241-85c99f8d-41ed-4731-adc4-c67b21bd6712.PNG) <br>
2와 8을 비교 한다, 자리바꿈 없음<br>

***

![Desc4](https://user-images.githubusercontent.com/97664446/170832242-e6203d39-52e2-46c8-aadc-937753fd3482.PNG) <br>
2와 4를 비교 한다, 자리바꿈 없음<br>

***

![Desc5](https://user-images.githubusercontent.com/97664446/170832243-8b4ed617-54f6-4cb5-a306-a17c194cdc15.PNG) <br>
2와 9를 비교 한다, 자리바꿈 없음<br>

***

![Desc6](https://user-images.githubusercontent.com/97664446/170832245-7b49d664-3309-40b1-b3cf-f19af6986529.PNG) <br>
2와 1을 비교 한다, 1이 더 작기 때문에 자리를 바꾼다 <br>

***

![Desc7](https://user-images.githubusercontent.com/97664446/170832246-2482d431-08b8-49eb-b692-31272157cc52.PNG) <br>
1과 6을 비교한다, 자리바꿈 없음 <br>

***

![Desc8](https://user-images.githubusercontent.com/97664446/170832247-61cd32de-6a43-42d5-8abc-e7c0d7a02c62.PNG) <br>
한 번 정렬이 실행되어 배열의 첫 번째 index에는 가장 작은 숫자가 오게된다 <br>
이제 기준이 되는 [index = 1]에 있는 숫자 5부터 다시 정렬 과정을 반복한다 

***

# Cord

```c++
#include <iostream>

using namespace std;

int main()
{
    int dataArr[] = { 2, 5, 7, 8, 4, 9, 3, 1, 6 };

    // Selection sort 실행
    for (int i = 0; i < 9; i++)
    {
        for (int j = i + 1; j < 9; j++)
        {
            // 왼쪽에서 부터 작은 수로 채운다
            if (dataArr[i] > dataArr[j])
            {
                int temp = dataArr[i];
                dataArr[i] = dataArr[j];
                dataArr[j] = temp;
            }
        }
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

# Result

![Result](https://user-images.githubusercontent.com/97664446/170831490-849fa753-a91a-4b89-ab6b-8622ac58956d.PNG)