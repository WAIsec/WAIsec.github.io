---
layout: single
title: "OS Design & Structure"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
OS의 design과 structure에 대해 학습한 내용입니다.

# OS design and structure

# System 구조

![OS 구조도](/images/OS-structure.png)

OS 구조도

# OS structure

## 1. User and other system programs

→ user program에서는 주로 system call 보다 high-level Application Programming Interface(API)를 이용합니다. 해당 API에서 system call을 수행하는 방식입니다. 이로 인해, 사용자가 직접 system call을 사용하지 않게 됩니다.

## 2. User interface

→ 사용자 레벨의 interface를 지칭합니다. GUI, CLI 등을 제공합니다.

## 3. System calls

### 정의

→ User mode에서 User는 특정 권한 없이 Kernel 접근이 제한적이며, 이로 인해 Kernel에서 제공하는 protected service가 제공됩니다. 이 경우, system call은 user mode에서 kernel 모드로 진입하기 위한 통로라고 생각하시면 됩니다. 단, User program은 system call을 직접 사용하지 않고 API를 통해 좀더 쉽게 사용합니다.  단방향적 성질을 가지고 있으며 user → kernel로의 진입만 가능합니다. kernel→user로의 진입은 ‘up-call’, ‘call-back’이라는 용어로 불립니다. 이러한 system call은 당연히 OS마다 다른 특징이 있습니다.

![system call의 동작 원리](/images/System-call.png)

system call의 동작 원리

 예를 들어, printf()라는 함수를 사용자 프로그램에서 호출한다고 생각해봅시다. 이때 printf()라는 API는 write()라는 system_call을 호출하게 되고 화면에 문자열을 출력하게 되는 것입니다.

# OS design

## 1. Design Consideration

→ OS는 복잡한 소프트웨어 이므로 구조를 신중히 고려하여야 합니다. 개발, 수정 및 디버깅, 유지보수, 확장에 대해서 고려를 해야하죠. OS의 Design은 목적과 구조로 구분될 수 있습니다.

### 목적에 따른 구분

→ 목적에 따른 구분으로는 General, 즉, 일반적인 목적과 Special한 특수 목적(군용 등)이 존재합니다.

### 구조에 따른 구분

→ 구조에 따른 구분으로는 Layering(계층 구조)와 Modularity(기능 단위)로 구분 지을 수 있습니다.

## 2. Kernel Structure

### Kernel이란?

→ ‘Kernel’이란, 하나의 컴퓨터에서 시작과 종료까지 항상 동작하는 하나의 프로그램을 가리키는 용어입니다. System의 모든 실행 권한을 보유하고 있으며, OS의 자원 할당, 하드웨어 인터페이스, 보안 등 핵심 기능을 수행합니다.

### Monolitic Kerenl

→ 단일 구조의 kernel이라고 생각하시면 됩니다.

**[특징]**

1. 모든 서비스가 같은 주소 공간(Address Space)에 위치합니다.
    
    → 이는 일부 수정이 전체에 영향을 미칠 수 있고 크기가 크기가 비교적 큽니다. 이로 인해 유지보수가 힘들며 한 모듈의 버그가 전체적인 영향을 끼친다는 단점이 존재합니다.
    
2. App들은 자신의 주소공간에 커널 코드 영역을 mapping하여 필요할 때 이용합니다.
    
    → 이로 인해, systemcall, kernel service 간의 데이터 전달 시 overhead가 적다는 장점이 있습니다. 
    

### Micro Kernel

→ Monolitic Kernel과 반대 개념이라 생각하시면 됩니다.

**[특징]**

1. 커널 서비스 기능에 따라 모듈화 하여 각각 도립된 주소 공간에서 실행됩니다. 해당 모듈들을 Server라고 부릅니다.
    
    → 서로 간에 의존성이 낮으며 각 서버들이 필요할 때만 동작합니다. 이는 독립적인 개발을 가능하게 하고, 유지 보수가 용이하고 안정적입니다. 하지만, 이러한 독립된 서버들은 독립된 프로세스로 구현되며, 이는 서버들이 동작할 때마다 Switching이 일어나 Overhead를 유발합니다.
    

### Hybrid Kernel

→ Monolitic Kernel + Micro Kernel 정도라고 생각하시면 됩니다. 현재 대다수가 이 구조를 이루고 있습니다.

## 3. System Software

### System Software란?

→ System software란, 사용자에게 편리한 개발 및 수행 환경을 제공하는 Software를 말합니다. C library도 하나의 시스템 소프트웨어라고 할 수 있습니다. 

### Type

- UI
- Program Loading & execution (link & compile)
- Communication (Networking)
- Background Services (e.g. AhnLab or Anti virus program)

<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>