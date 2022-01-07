---
layout: single
title: "Assembly Lang - Stack"
category: [Assembly]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
어셈블리언어에서의 스택 조작에 대해 학습한 내용입니다.

# 스택 조작

## Stack 이란?

→ Stack memory는 데이터가 일시적으로 저장되는 메모리 공간을 지칭합니다. 전형적인 FILO 방식을 따르는 구조입니다. C의 프로그램에서 컴파일된 함수는 초기에 초기화되지 않은 변수에 대해 스택 프레임(Stack Frame)이라 불리는 스택의 공간을 할당합니다. 스택의 상단 주소는 ESP 레지스터에 저장됩니다.

## Stack의 연산

### PUSH

→ Push 연산은 32bit 주소 공간에서 DWORD 크기만큼 스택 상단 주소를 줄인 후 피연산자의 값을 저장합니다. 

> ***왜 Stack 상단 주소를 줄이는 걸까요?***
> 
> 
> 
> → 추가적인 데이터를 넣는데 왜 스택의 상단을 줄인다는 표현을 사용하는 걸까요? 그 이유는 Memory 구조에 있습니다. Memory는 다음과 같이 이루어져 있습니다.
> 
> ![MemoryStructure.png](/images/MemoryStructure.png)
> 
> 이렇게 표현되어져 있는 상태에서 새로운 데이터를 저장하기 위해 stack의 크기를 늘린다는 것은 메모리 주소상 주소를 낮춘다는 것입니다. 
> 

```wasm
; 예를 들어보겠습니다. 현재 ESP에 저장되어있는 주소가 002FFFCh 인 경우로 가정합시다.
push 1    ; 이 명령어를 수행하는 순간 ESP는 002FFF8h가 되고 해당 메모리에 1이라는 값을 저장할 것입니다.
```

### POP

→ POP은 스택 상단에서 값을 가져온 후 피연산자에 표시된 레지스터 또는 메모리 공간에 저장합니다. 그러고 나서 ESP는 DWORD 사이즈만큼 증가됩니다. (← 이제 무슨 의미인지 알 것입니다.)

```wasm
; 이전의 예시에서 상황을 이어가겠습니다.
pop eax      ; 해당 명령어가 수행되는 순간 이전에 ESP에 있는 주소 002FFF8h가 4증가되어 002FFFCh가 되고, 저장되어있던 1이 EAX에 저장됩니다.
```

### PUSHA / PUSHAD

→ A는 ALL의 약자이며 모든 범용 레지스터들을 순서대로 스택에 push하는 것을 뜻합니다.  A의 경우 16bit 피연산자를 위한 것이고, AD는 32bit 피연산자를 위한 것입니다.

### POPA / POPAD

→ 위와 같은 수행을 pop으로 하는 것입니다.

### PUSHF / POPF

→ EFLAGS를 스택으로 넣고 빼는 행위입니다.

### ENTER

→ 일반적으로 서브루틴(Sub routine)의 시작 부분에 쓰이며, 해당 루틴을 위한 스택 프레임을 만드는 데 사용됩니다. 

ENTER 8, 0     ; 은 대략 다음과 동일한 역할을 수행합니다.

```wasm
push ebp          ; 현재 ebp 값을 저장
mov  ebp, esp     ; 현재 스택을 ebp로 지정
add  esp, 8       ; 8byte 크기의 스택 프레임 생성
```

### LEAVE

→ ENTER가 만든 스택 프레임을 원복하는 데 사용됩니다.

<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>