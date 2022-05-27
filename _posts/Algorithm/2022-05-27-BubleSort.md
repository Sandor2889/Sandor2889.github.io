---
layout: single
title: "버블 정렬"
categories: Algorithm
tag: [Algorithm, Sort]
toc: true
toc_sticky: true
---

# Description
기준이 되는 숫자와 바로옆의 숫자를 비교하여  <br>
더 작은 숫자를 앞으로 보내주는 것을 반복하는 정렬 방법이다. <br>
시간 복잡도는 O(N^2) 이다.

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
