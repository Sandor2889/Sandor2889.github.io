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

![Desc1](https://user-images.githubusercontent.com/97664446/170835406-f0a6849a-6e8f-473a-8477-b2a972152502.PNG) <br>
2와 5중에 2가 더 작은 수이기 때문에 다음으로 넘어간다

***

![Desc2](https://user-images.githubusercontent.com/97664446/170835407-0c7cc32b-b039-4a4d-970f-2a1755de6287.PNG) <br>
2와 7을 비교 한다, 자리바꿈 없음<br>

***

![Desc3](https://user-images.githubusercontent.com/97664446/170835399-0cb6503d-b701-4c61-9b79-86dff5214da3.PNG) <br>
2와 8을 비교 한다, 자리바꿈 없음<br>

***

![Desc4](https://user-images.githubusercontent.com/97664446/170835400-ae8a8ace-6730-4e00-852f-142e446436ac.PNG) <br>
2와 4를 비교 한다, 자리바꿈 없음<br>

***

![Desc5](https://user-images.githubusercontent.com/97664446/170835401-83874e9d-139a-4f42-86a9-3e9572dc99b2.PNG) <br>
2와 9를 비교 한다, 자리바꿈 없음<br>

***

![Desc6](https://user-images.githubusercontent.com/97664446/170835402-a60d4c9b-8cb4-4e5e-a3ee-82fb4dfd3a59.PNG) <br>
2와 3을 비교한다, 자리바꿈 없음 <br>

***

![Desc7](https://user-images.githubusercontent.com/97664446/170835403-8846bd8a-2ad5-493b-bcfb-24493bfaf7c7.PNG) <br>
2와 1을 비교 한다, 1이 더 작기 때문에 자리를 바꾼다 <br>

***

![Desc8](https://user-images.githubusercontent.com/97664446/170835404-be7b5bd7-6956-48e8-9497-e98fa7709411.PNG) <br>
1과 6을 비교한다, 자리바꿈 없음 <br>

***

![Desc9](https://user-images.githubusercontent.com/97664446/170835405-6952b5d9-4622-45a8-bd3e-39c0e43cc041.PNG) <br>
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