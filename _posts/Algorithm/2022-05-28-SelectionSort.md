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


![Desc1](https://user-images.githubusercontent.com/97664446/170831491-b08a3ae6-d924-4857-8eee-303008bdb677.PNG) <br>
2와 5중에 2가 더 작은 수이기 때문에 다음으로 넘어간다

***

![Desc2](https://user-images.githubusercontent.com/97664446/170831492-c1a610c6-c37f-4c47-92fb-5bbeac92c55c.PNG) <br>
2와 7을 비교 한다, 자리바꿈 없음<br>

***

![Desc3](https://user-images.githubusercontent.com/97664446/170831493-ca9e9130-2b95-49b4-89f9-7f47fae9e5b5.PNG) <br>
2와 8을 비교 한다, 자리바꿈 없음<br>

***

![Desc4](https://user-images.githubusercontent.com/97664446/170831494-a1f2e677-fe0c-4f4a-833c-01db8f24a736.PNG) <br>
2와 4를 비교 한다, 자리바꿈 없음<br>

***

![Desc5](https://user-images.githubusercontent.com/97664446/170831495-e6de32b3-6684-459c-8b1a-7d478775f605.PNG) <br>
2와 9를 비교 한다, 자리바꿈 없음<br>

***

![Desc6](https://user-images.githubusercontent.com/97664446/170831496-b550009a-5dc3-4195-a60b-f9fb1a354391.PNG) <br>
2와 1을 비교 한다, 1이 더 작기 때문에 자리를 바꾼다 <br>

***

![Desc7](https://user-images.githubusercontent.com/97664446/170831497-f61da339-6192-497a-8d04-1e8ce796b42c.PNG)
1과 6을 비교한다, 자리바꿈 없음 <br>

***

![Desc8](https://user-images.githubusercontent.com/97664446/170831498-a60c21f5-4f23-4435-b387-b2069d46268f.PNG)
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