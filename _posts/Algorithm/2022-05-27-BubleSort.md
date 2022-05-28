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

![Desc1](https://user-images.githubusercontent.com/97664446/170823494-68943511-dc60-4891-b8fd-ffe410dd8db3.PNG) <br>
이렇게 2와 5의 숫자를 비교한다 <br>
큰 수가 오른쪽으로 가야하기 때문에 자리를 바꾸지 않고 다음으로 넘어가면 된다 

***

![Desc2](https://user-images.githubusercontent.com/97664446/170823495-ea29b0b0-fd1f-464c-af6a-14d63be19efd.PNG) <br>
다음으로 5와 7을 비교한다 <br>
마찬 가지로 큰 수인 7이 오른쪽에 있기 때문에 다음으로 넘어간다 

***

![Desc3](https://user-images.githubusercontent.com/97664446/170823496-0254ddec-5df5-4ee0-bf34-c2b02167d3cc.PNG) <br>
위와동일 

***

![Desc4](https://user-images.githubusercontent.com/97664446/170823498-cf6e8547-ed5d-4158-8dd7-48bf68f0e95a.PNG) <br>
이번에는 왼쪽에 있는 8이 4보다 크기 때문에 서로 자리를 바꾼다

***

![Desc5](https://user-images.githubusercontent.com/97664446/170823500-818385a6-2e25-4703-bd2d-57e3437e4bcf.PNG) <br>
4와 8의 자리가 바뀌었고 다음 두 수를 계속해서 비교해 나간다

***
![Desc6](https://user-images.githubusercontent.com/97664446/170823504-84a5f9b7-166a-47c1-9a89-88de4c63facc.PNG) <br>
9와 1의 자리를 바꾼다 

***
![Desc7](https://user-images.githubusercontent.com/97664446/170823505-837ba882-748d-424b-89a5-dc0c57d9befd.PNG) <br>
9와 6의 자리를 바꾼다 

***

![Desc8](https://user-images.githubusercontent.com/97664446/170823506-fb5e50ef-4ec6-4ed8-9eb2-4055ec57f32d.PNG) <br>
이로써 정렬이 한번 이루어져 맨 끝 자리는 제일 큰 수(9)로 확정된다 <br>
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
