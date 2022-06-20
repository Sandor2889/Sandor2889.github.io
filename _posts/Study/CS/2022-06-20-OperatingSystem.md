---
layout: single
title: "Chapter3 운영체제 : 프로세스와 스레드"
categories: StudyCS
tag: [Study, CS, OperatingSystem]
---

# <span style="color:orange"> 프로세스와 스레드 </span>
프로세스는 컴퓨터에서 실행되고 있는 프로그램을 말하며 CPU 스케줄링의 대상이 되는 작업이라는 용어와 같은 의미로 쓰인다. 스레드는 프로세스 내 작업의 흐름을 지칭한다.

<p align= "center"> 
<img src="https://user-images.githubusercontent.com/97664446/174625202-ed97eec9-8317-4186-9f76-710f88c96f45.PNG" alt="process">
</p>

프로그램이 메모리에 올라가면 프로세스가 되는 인스턴스화가 일어나고, 이후 운영체제의 CPU 스케줄러에 따라 CPU가 프로세스를 실행한다.

## <span style="color:orange"> 프로세스와 컴파일 과정 </span>
프로세스는 프로그램으로부터 인스턴스화된 것을 말한다. 예를 들어 프로그램은 구글 크롬 프로그램과 같은 실행 파일이며, 클릭하면 크롬 프로세스가 시작된다.
프로그램은 컴파일러가 컴파일 과정을 거쳐 컴퓨터가 이해할 수 있는 기계어로 번역되어 실행될 수 있는 파일이 되는 것을 의미한다  
컴파일 과정(c언어 기반)은 이와 같이 진행된다.

<p align= "center"> 
<img src="https://user-images.githubusercontent.com/97664446/174626316-a5b6a943-d365-4ba0-b40e-320de4075901.PNG" alt="ComfilePNG">
</p>

### <span style="color:orange"> 전처리 </span>
소스 코드의 주석을 제거하고 #include 등 헤더 파일을 병합하여 매크로를 치환한다.

### <span style="color:orange"> 컴파일러 </span>
오류 처리, 코드 최적화 작업을 하며 어셈블리어로 변환한다.

### <span style="color:orange"> 어셈블러 </span>
어셈블리어는 목적 코드로 변환된다. 이때 확장자는 운영체제마다 다른데 리눅스에서는 .o이다. 예를 들어 가영.c라는 파일을 만들었을 때 가영.o라는 파일이 만들어진다.

### <span style="color:orange"> 링커 </span>
프로그램 내에 있는 라이브러리 함수 또는 다른 파일들과 목적 코드를 결합하여 실행 파일을 만든다. 실행 파일의 확장자는 .exe 또는 .out이라는 확장자를 갖는다.  

### <span style="color:orange"> 정적 라이브러리와 동적 라이브러리 </span>
라이브러리는 `정적 라이브러리`와 `동적 라이브러리`로 나뉜다.  
`정적 라이브러리`는 프로그램 빌드 시 라이브러리가 제공하는 모든 코드를 실행 파일에 넣는 방식이며, 시스템 환경 등 외부 의존도가 낮고 코드 중복 등 메모리 효율성이 떨어지는 단점이 있다.  
`동적 라이브러리`는 프로그램 실행 시 필요할 때만 DLL이라는 함수 정보를 통해 참조하는 방식이며, 메모리 효율성에서의 장점과 외부 의존도가 높아진다는 단점이 있다.  

## <span style="color:orange"> 프로세스의 상태 </span>

