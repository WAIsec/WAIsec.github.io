---
layout: single
title: "Register"
category: [Assembly]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---

Assembly 언어에서의 변수 역할! Register에 대해 학습한 내용입니다.

## 개념

 극소량의 데이터나 연산 처리 중 일시적인 저장을 위해 사용되는 고속의 저장 공간을 레지스터라고 합니다. 이러한 레지스터들은 어셈블리 언어에서 변수로 생각하면 쉽습니다. 물론 변수라는 의미로 원론적으로 접근했을 떄 실제 변수와는 개념 자체가 완전히 다르지만, 쉽고 친근하게 접근하기 위해서는 변수라고 생각하는 것이 가장 효율적입니다. 또한 모든 레지스터가 일반 변수로 취급되는 것은 아닙니다. 각 레지스터들은 지정된 목적이 있습니다.

### 레지스터 종류

1. 범용 레지스터
2. 세그먼트 레지스터
3. 플래그 레지스터
4. 인스트럭션 포인터

 우리는 이 중에서 1. 범용 레지스터를 리버싱 과정에서 사용할 것 입니다. 

## 범용 레지스터

범용 레지스터에는 8개의 레지스터가 있습니다. 그리고 각 레지스터들은 지정된 목적에 따라 WORD 크기 또는 16bit로 저장됩니다. 

E: 32bit 확장을 뜻 합니다. 64bit는 RAX와 같이 표기됩니다.

.

- EAX
    
    → EAX에서 A는 Accumulator의 약자이며, 산술 계산에 주로 쓰이고 함수의 리턴 값을 저장하는 레지스터입니다. 쉽게 생각했을 때 가장 많이 쓰이는 변수라고 생각하면 될 것 같습니다.  EAX의 하위 16bit를 AX라고 하며, AX의 상위 8bit를 BH, 하위 8bit를 AL이라고 부릅니다.
    
- EDX
    
    → EDX에서 D는 Data의 약자입니다. EAX와 비슷한 역할이지만 리턴 값은 받지 않습니다. 이 레지스터 또한 각종 연산에 사용되며 곱하기나 나누기 연산에서 좀더 복잡한 연산이 필요할 때 덤으로 쓰이는 녀석입니다. EDX의 하위 16bit를 DX라고 하며, DX의 상위 8bit를 BH, 하위 8bit를 DL이라고 부릅니다.
    
- ECX
    
    → ECX에서 C는 Count의 약자입니다. 이름과 역할이 연관되어 기억하기 쉽습니다.  쉽게 생각한다면 for문에서 for(i=n; i>0; i - - )에서 i의 역할이라고 보시면 됩니다. 여기서 중요한 것은 전체 횟수를 초기에 저장하고 이후 i- - 를 통해 0이 된 경우 반복문을 마친다는 것입니다. 무언가를 카운팅할 필요가 없는 경우에는 일반 변수처럼 사용하여도 무방합니다. ECX의 하위 16bit를 CX라고 하며, CX의 상위 8bit를 BH, 하위 8bit를 BL이라고 부릅니다.
    
- EBX
    
    → EAX, EDX, ECX가 부족할 때 사용되기도 하며, 특정 목적을 가지고 만들어진 레지스터가 아니라서 레지스터가 하나쯤 더 필요하거나 공간이 필요할 때 등 적당한 용도를 프로그래머나 컴파일러가 알아서 만들어서 사용합니다. 실제로 A, C, D가 존재해서 별다른 의미 없이 B를 넣어 EBX라고 만든 것 입니다. EBX의 하위 16bit를 BX라고 하며, BX의 상위 8bit를 BH, 하위 8bit를 BL이라고 부릅니다.
    
- ESI, EDI
    
    → ESI(Source Index), EDI(Destination Index)라고 정의됩니다. 즉, 시작 인덱스와 도착지 인덱스 입니다. 이름에서 알 수 있듯이 일반적인 산술 연산보다 문자열이나 각종 반복 데이터를 처리 또는 메모리를 옮기는 데 많이 사용됩니다. 데이터는 항상 ESI→EDI로 복사된다고 생각하면 편합니다. Source와 Destination으로 기억합시다.
    
- ESP, EBP
    
    → 다음은 ESP와 EBP 입니다. 이 레지스터들은 스택 영역과 관련된 레지스터 입니다. ESP는 Stack Pointer로 범용적으로 SP라고 불립니다.  ESP는 현재 스택의 최상단 주소값을 저장하고 있는 레지스터 입니다. EBP는 Base Pointer로 해당 스택 프레임의 Base 주소를 기억하는 레지스터입니다. 스택 프레임이란 쉽게 말하자면 하나의 함수라고 생각하시면 편합니다. C언어로 예를 들자면 main이라는 큰 스택  프레임 안에 세부적인 함수들이 그 함수들만의 스택 프레임을 꾸리고 존재하는 것이라고 볼 수 있습니다. 그럼, 여러개의 함수들이 있을 때 EBP는 어떤 값을 저장하나요? 라고 생각하실 수 있는데 EBP는 현재 진행 중인 명령어가 존재하는 함수의 스택 프레임을 저장합니다.
    

## 인스트럭션 포인터(IP)

**정의 : 코드를 실행할 때 시스템은 코드가 있는 위치를 식별하여야 하는데 이때 인스트럭션 포인터(IP) 레지스터는 다음에 실행할 어셈블리 명령이 저장되는 메모리 주소를 포함합니다. Program Counter(PC)의 역할이라고 생각하면 됩니다.**

## 플래그 레지스터(Flag Register)

→ 실행된 코드의 시스템 상태와 논리적 결과는 플래그 레지스터에 저장됩니다. 각각의 플래그 레지스터는 각각의 목적을 가지고 있습니다. 이중 필요한 플래그들에 대해서만 다뤄보겠습니다.

1. CF : 캐리 플래그이며 추가 작업에 비트를 전달해야 할 때 설정됩니다. 예를 들어 뺄셈 연산에서 비트를 앞자리에서 빌리거나, 덧셈 연산에서 앞자리로 연산의 결과에서 발생한 carry 값이 존재할 때 1로 설정됩니다.
2. PF : 패리티 플래그로 이 플래그는 설정 비트 수가 마지막 명령 작업에서 홀수인지 짝수인지를 나타내는 비트입니다. 짝수일 경우 1, 홀수일 경우 0으로 설정됩니다.
3. AF : 조정 플래그라하며, BCD(이진 부호화 십진수)에서 사용됩니다. 이 플래그는 낮은 니블(Nibble: 4bit 크기의 데이터 unit)에서 높은 니블로 캐리가 발생하거나 바이트의 높은 니블에서 낮은 니블로 빌림이 발생할 때 1로 설정됩니다.
4. ZF : 제로 플래그로 아마 가장 많이 보게 될 플래그인 것 같습니다. 이 플래그는 마지막 명령 연산의 결과가 0일 때 1로 설정되는데, 주로 비교 연산이나 조건문 등에서 많이 활용되는 상태 플래그입니다.
5. SF : 사인 플래그입니다. 이 플래그는 마지막 명령 연산의 결과가 음수일 때 1로 설정됩니다.
6. TF : 트랩 플래그로 이는 디버깅할 때 사용됩니다. 중단점이 발생할 때 설정되며, 해당 플래그를 설정 시 모든 명령어에서 예외가 발생할 수 있으므로 디버깅 도구가 단계별 디버깅을 제어할 수 있습니다.
7. IF : 인터럽트 플래그입니다. 이 플래그가 설정되면 프로세서는 인터럽트에 응답하며, 인터럽트는 오류, 외부 이벤트 또는 예외가 하드웨어 또는 소프트웨어로부터 트리거되는 인스턴스 입니다.
8. DF : 디렉션 플래그 입니다. 이 플래그가 설정되면 해당 데이터는 메모리에서 거꾸로 읽힙니다.
9. OF : 오버플로 플래그입니다. 산술 연산 결과 Overflow가 발생 시 설정됩니다.

기타 다른 플래그(IOPL, NT, RF, VM, AC, VIF, VIP, ID)가 있지만, 주로 다룰 내용이 아니므로 생략했습니다.

많은 플래그들이 존재하지만 주로 사용될 플래그들은 CF, SF, ZF, OF, PF 정도 입니다.

## 세그먼트 레지스터

메모리의 각 세그먼트를 가리키는 레지스터입니다.

### Segment

프로그램이 로드되면 OS는 실행 파일을 메모리에 매핑하게 되는데, 실행 파일에는 각 세그먼트를 매핑하는 데이터가 포함되어 있습니다. 세그먼트의 영역들은 다음과 같습니다.

- 스택 세그먼트(SS)
    
    → 런타임 함수 변수 및 기타 처리된 데이터를 포함합니다.
    
- 코드 세그먼트(CS)
    
    → 실행 코드를 포함합니다.
    
- 데이터 세그먼트(DS)
    
    → 상수, 문자열 및 전역 변수와 같은 데이터 바이트가 포함되어 있습니다.
    
- 엑스트라 세그먼트(ES)
    
    → DS와 유사하지만 변수 간 데이터를 이동하는 데 일반적으로 쓰입니다.
    
- F 세그먼트(FS)
    
    → 프로세스 정보를 가리킵니다.
    
- G 세그먼트(GS)
    
    → 스레드 정보를 가리킵니다.

 <a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>