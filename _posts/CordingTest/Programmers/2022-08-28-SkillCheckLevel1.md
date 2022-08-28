---
layout: single
title: "[Programmers] Skill Check Level1"
categories: Programmers
tag: [Programmers, string]
toc: true
toc_sticky: true
---

이번 30일 코딩테스틀을 대비하여 마치 시험치듯이  
프로그래머스에 있는 스킬체크레벨 단계별로 풀어보기로 하였다.  
부정행위 감지한다는 문구가 있어 도중에 스샷은 찍진 못했지만 기억다는대로 문제를 적어보겠다.  

# Question 1
입력 n이 주어진다.  
n이 3이면 "수박수", 4면 "수박수박" 과 같은 패턴으로 출력된다.  
n은 10000이하의 자연수이다.  

***

# Solution 1
- 입력값을 for에서 홀수와 짝수로 나눈다.  
- string arr[] = {"박", "수"}; 를 선언하여 홀수면 "수", 짝수면 "박" 을 answer에 더해주었다.

***

# Cord 1
```c++
#include <string>
#include <vector>

using namespace std;

string arr[2] = {"박", "수"};

string solution(int n) {
    string answer = "";
    for(int i = 1; i <= n; i++)
    {
        int temp = i % 2;
        answer += arr[temp];
    }

    return answer;
}
```

처음엔 char 배열에 한글을 넣었지만 자꾸 크기에러가 나서 string으로 선언했더니 되었다.  
한글의 크기와 알파벳의 크기가 다르다는것을 알게되었다.  

***

# Question 2
숫자 배열이 주어지고 이 배열에서 중복되지 않는 인덱스로 숫자 두개를 뽑아 만들 수 있는  
모든 수를 배열에 넣고 오름차순으로 출력하라.  

***

# Solution 2
- 이중 for문으로 입력으로 주어지는 배열에서 중복되지 않도록 숫자를 고른다.  
- 모든 숫자에 대해 덧셈을 실시하도록 하고 결과 값을 배열에 넣는다.
- 나는 유니크 메서드를 쓰는 법을 까먹어 따로 중복검사를 하고 나서 배열에 넣었다...

***

# Cord 2
```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

 vector<int> answer;

bool checkDuple(int sum)
{
    for(int k = 0; k < answer.size(); k++)
    {
        if (sum == answer[k]) 
        { 
            return true;
        }
    }
    return false;
}

vector<int> solution(vector<int> numbers) { 
    for(int i = 0; i < numbers.size(); i++)
    {
        for(int j = i + 1; j < numbers.size(); j++)
        {
            int sum = numbers[i] + numbers[j];

            // 중복 검사
            if (!checkDuple(sum))
            {
                answer.push_back(sum);
            }
        }
    }

    // 오름차순 정렬
    sort(answer.begin(), answer.end());
    return answer;
}
```

***

# Result 
![Result](https://user-images.githubusercontent.com/97664446/187074838-078c9ea9-2ea1-4363-aa18-5b1641e6bb96.PNG)
