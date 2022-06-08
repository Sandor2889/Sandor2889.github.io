---
layout: single
title: "퀵 정렬"
categories: Algorithm
tag: [Algorithm, Sort]
toc: true
toc_sticky: true
---

# Description
`퀵 정렬은` 두 개로 분할하여 정렬을 하기 때문에 굉장히 빠르게 정렬을 수행 할 수 있다. <br>
하지만 이미 정렬된 수의 경우 최악의 시간 복잡도를 가지게 된다. <br>
`시간 복잡도 = O(N*logN), 최악 = O(N^2)` 이다. <br>

`pivot`을 기준으로 왼쪽에서부터 `큰 수 = 파랑`을 찾고 <br>
오른쪽에서부터 `작은 수 = 초록`을 찾아 서로 교환한다. <br>
엇갈릴 경우 작은 수 와 pivot을 서로 교환한다. <br>

위 규칙을 두고 한번 수행해보자. <br>

![Desc1](https://user-images.githubusercontent.com/97664446/172620169-d67e1882-b0c2-4ff1-b526-5de523eb1fae.PNG) <br>
우선 pivot 맨 좌측 부터 시작하여 숫자 2가 된다. <br>
2보다 큰 값을 pivot 다음 수부터 찾아보면 5가 나온다. <br>
작은 수는 맨 끝자리부터 찾아가보면 1이 나왔다. <br>
그러면 5와 2의 자리를 서로 바꾼다. <br>

![Desc2](https://user-images.githubusercontent.com/97664446/172620175-12b54b5c-647c-4eac-b820-1b12a4efd671.PNG) <br>
이번엔 큰 수 7과 작은 수 1이 나왔다. <br>
서로간의 위치가 `엇갈리다` 혹은 `교차`하게 되었다. <br>
그러면 작은 수의 위치와 pivot의 자리를 바꾼다. <br>

![Desc3](https://user-images.githubusercontent.com/97664446/172620178-65298e6f-0eee-4eb1-bc4f-2d5228f54031.PNG) <br>
이제 2를 기준으로 좌측은 2보다 작은 수, 우측은 2보다 큰수로 분할이 되어진다. <br>
이렇게 정상적으로 나뉘면 정렬이 잘 된것이다. <br>
좌측과 우측을 나눠 계속해서 정렬을 실행한다. <br>
좌측의 pivot은 1이다. <br>

![Desc4](https://user-images.githubusercontent.com/97664446/172620181-0b0df83e-a213-4cd8-8ff5-52c13e88c9a4.PNG) <br>
원소가 한개만 존재하게되면 건너 뛰면된다. <br>
그러므로 1이 정렬이 되어진다. <br>
이제 우측정렬을 실행한다 <br>

![Desc5](https://user-images.githubusercontent.com/97664446/172620185-49c2ad8d-2126-4790-b7c5-498f53104f78.PNG) <br>
우측의 pivot은 7이고 큰수 8과 작은 수 6을 자리를 바꾼다. <br>

![Desc6](https://user-images.githubusercontent.com/97664446/172620189-bf1658ca-c00c-4cad-a594-0746ec23f39d.PNG) <br>
계속해서 pivot은 7이고 큰수 9와 작은 수 5의 자리를 바꾼다. <br>

![Desc7](https://user-images.githubusercontent.com/97664446/172620192-3fdf21b5-6385-4b0d-b71d-7fc3d3244f01.PNG) <br>
pivot은 7이고 큰수 9와 작은 수 3이 엇갈려 pivot과 작은 수의 자리를 바꾼다. <br>

![Desc8](https://user-images.githubusercontent.com/97664446/172620195-efe32b40-5038-4868-a475-4d13e413537a.PNG) <br>
7은 정렬이 이루어 지고 좌측의 pivot 3과 우측의 pivot 9가 만들어진다. <br>
항상 좌측 부터 실행한다. <br>

![Desc9](https://user-images.githubusercontent.com/97664446/172620197-8a24aac2-56ab-441c-9edb-a5b2faf675cd.PNG) <br>
pivot은 3이고 큰 수 4와 작은 수는 찾지 못하여 자기 자신을 가리키게 된다. <br>
그러면 엇갈리게 되어 혼자 자리를 바꾸게되어 3에 대한 정렬이 이루어진다. <br>

![Desc10](https://user-images.githubusercontent.com/97664446/172620198-bad5cd7e-3b5c-4a1c-ab68-000c97126b32.PNG) <br>
3의 기준으로 우측정렬이 실행이 되어 pivot은 4가 된다. <br>
3때와 마찬가지로 4 혼자 자리를 바꾸고 정렬이 이루어 진다. <br>

![Desc11](https://user-images.githubusercontent.com/97664446/172620199-3aa3a509-130b-46bf-b581-2d94f5631fee.PNG) <br>
4를 기준으로 우측 정렬이 실행이 되어 pivot은 6이 된다. <br>
큰 수를 찾지 못하게 되어 5에서 멈추게 되고 작은 수 5와 겹치게 된다. <br>
엇갈리는 조건과 동일시하여 작은 수 5와 6의 자리를 바꾼다. <br>

![Desc12](https://user-images.githubusercontent.com/97664446/172620201-b6d7c7f1-3cd7-47eb-9f9e-5cf8a5584613.PNG) <br>
6의 정렬이 이루어지고 좌측정렬 한 개의 데이터인 5가 정렬이 될 것이다. <br>

![Desc13](https://user-images.githubusercontent.com/97664446/172620204-e766fcbb-ceaf-4249-bec9-f3cb9d97e8cc.PNG) <br>
이제 우측 정렬에서 pivot은 9이고 큰 수가 없어 8, 작은 수도 8이 된다. <br>
엇갈리는 조건으로 실행되어 8과 9의 자리를 바꾸가 9는 정렬이 이루어 진다. <br>

![Desc14](https://user-images.githubusercontent.com/97664446/172620207-e3c93d62-9131-41c1-89ea-011fddc55e83.PNG) <br>
이로써 모든 데이터가 정렬이 이루어 진다. <br>

퀵 정렬은 처음에 정렬 방식이 이해하는데 오래 걸려 이렇게 끝까지 정렬되어지는 모습을 찍었다. <br>
하고나면 코드도 간단한 편이고 크게 어려움은 없었다. <br>
***

# Cord
```c++
#include <iostream>

#define LENGTH 9 // 배열의 길이

using namespace std;

void quickSort(int* _data, int _start, int _end)
{
	// 원소가 1개 인 경우
	if (_start >= _end) { return; }

	int pivot = _start;
	int left = _start + 1;
	int right = _end;
	int temp;

	while (left <= right)
	{
		// pivot 보다 큰 수 찾기
		while (_data[pivot] > _data[left] && left < _end)
		{
			left++;
		}

		// pivot 보다 작은 수 찾기
		while (_data[pivot] < _data[right] && right > _start)
		{
			right--;
		}

		// 자리 교환
		if (left >= right)	// 엇갈렸을 때 또는 같을때
		{
			temp = _data[pivot];
			_data[pivot] = _data[right];
			_data[right] = temp;
		}
		else	// 엇갈리지 않았을때
		{
			temp = _data[left];
			_data[left] = _data[right];
			_data[right] = temp;
		}
	}
	quickSort(_data, _start, right - 1);	// 좌측 정렬
	quickSort(_data, right + 1, _end);	// 우측 정렬
}

int main()
{
    int dataArr[LENGTH] = { 2, 5, 7, 8, 4, 9, 3, 1, 6 };

	// 퀵 정렬 실행
	quickSort(dataArr, 0, LENGTH - 1);

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

#  Result
![Result](https://user-images.githubusercontent.com/97664446/172623602-bbb929a9-1e02-4a99-89b1-b237e0fd0e89.PNG)