---
layout: single
title: "버블 정렬"
categories: Algorithm
tag: [Algorithm, Sort]
toc: true
toc_sticky: true
---

# Description
기준이 되는 숫자와 바로옆의 숫자를 비교하여 더 작은 숫자를 앞으로 보내주는 것을 반복하는 정렬 방법이다 <br>
시간 복잡도는 O(N^2) 이다

![Desc1](https://user-images.githubusercontent.com/97664446/170835760-53b71ea1-4664-4e2a-bee4-42477311a89d.PNG) <br>
이렇게 2와 5의 숫자를 비교한다 <br>
큰 수가 오른쪽으로 가야하기 때문에 자리를 바꾸지 않고 다음으로 넘어가면 된다 

***

![Desc2](https://user-images.githubusercontent.com/97664446/170835763-48deaa7a-47e9-4365-b047-b7d01d8da90d.PNG) <br>
다음으로 5와 7을 비교한다 <br>
마찬 가지로 큰 수인 7이 오른쪽에 있기 때문에 다음으로 넘어간다 

***

![Desc3](https://user-images.githubusercontent.com/97664446/170835764-301fb3b9-2587-4061-8467-f7dfe9068f7c.PNG) <br>
7과 8을 비교한다, 자리바꿈 없음 <br>

***

![Desc4](https://user-images.githubusercontent.com/97664446/170835766-1a89a2f2-501b-4c1c-9192-69452c5c1261.PNG) <br>
이번에는 왼쪽에 있는 8이 4보다 크기 때문에 서로 자리를 바꾼다

***

![Desc5](https://user-images.githubusercontent.com/97664446/170835767-ac0e6357-7afe-4601-bfdf-c48236f19247.PNG) <br>
4와 8의 자리가 바뀌었고 8과 9를 비교한다, 자리바꿈 없음 <br>

***
![Desc6](https://user-images.githubusercontent.com/97664446/170835768-eb479b9f-0bae-45c1-aa4d-79a2a1917659.PNG) <br>
9와 3의 자리를 바꾼다 

***
![Desc7](https://user-images.githubusercontent.com/97664446/170835769-796fd325-dcc0-4b05-bf65-45ca21935aae.PNG) <br>
9와 1의 자리를 바꾼다 

***

![Desc8](https://user-images.githubusercontent.com/97664446/170835772-a13471e9-5a22-487c-9ab5-ac0bb70b8061.PNG) <br>
9와 6의 자리를 바꾼다>
***
![Desc9](https://user-images.githubusercontent.com/97664446/170835773-72217ce9-7a09-45e8-a656-6098f4011711.PNG)

이로써 정렬이 한 번 이루어져 맨 끝 자리는 제일 큰 수(9)로 확정된다 <br>
계속해서 위와 같은 과정을 반복하여 끝에서 부터 한 자리씩 수를 정렬해 나가면 된다

***

# Cord
```c++
#include <iostream>

using namespace std;


int main()
{
    int dataArr[] = { 2, 5, 7, 8, 4, 9, 3, 1, 6 };

    // Buble sort 실행
    for (int i = 0; i < 9; i++)
    {
        for (int j = 0; j < 8 - i; j++)
        {
            if (dataArr[j] > dataArr[j + 1])
            {
                int temp = dataArr[j];
                dataArr[j] = dataArr[j + 1];
                dataArr[j + 1] = temp;
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
    return 0;
}
```

***

# Result
![Result](https://user-images.githubusercontent.com/97664446/170705732-0a4053e3-bfe4-49b6-8aaf-df5f913df807.PNG)
