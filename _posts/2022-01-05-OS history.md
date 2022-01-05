---
layout: single
title: "OS history"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
먼저 OS 자체를 배우기에 앞서 OS의 역사에 대해 알아볼 것 입니다.

현재 우리가 사용하고 있는 OS들 Windows, Linux, Mac OS 등등 이러한 OS들은 어떠한 과정을 거쳐 발전했을까요?

### **Origin of computers**

여러분은 Computer의 기원에 대해 알고 계신가요?

우리가 알고 있는 Computer는 ‘Compute’ 라는 ‘계산하다’ 라는 단어에서 유래되었습니다.  말 그대로 계산해주는 자!

이러한 Computer는 2차 세계대전에서 암호 해석, 미사일 탄도 분석, 물리 계산에 사용되어졌습니다. 

### ***Moore’s Law란?***

![Moore's Law](/images/2022-01-05-OS-history/Moore's-Law.png)

 ‘Moore’s Law(무어의 법칙)’이 무엇일까요? Computer는 급격한 변화를 거듭해왔습니다. 이에 대해, Morre라는 학자는 2년마다 Computer의 연산속도가 약 2배정도 늘어난다는 이론을 주장했는데요. 이것이 바로 ***‘Moore’s Law’*** 입니다.

### OS(Operating System)의 진보

1. **Batch Processing System**
    - 일괄 처리는 아주 단순한 OS 개념으로 시작한 작업(job)이 끝나야 다음 작업을 수행할 수 있습니다.
    - 주로 복잡한 계산에 사용됩니다.
    - 펀치카드를 제출하고 이를 메모리에 적재하여 수행하는 순서로 이루어지며
    - 사람이 직접 Job Scheduling을 수행합니다. → 이는 사람이 직접 다음 작업을 실행하기 때문에 Job swiching issue를 유발합니다.
    - 또한 기계적인 I/O 장치와 전기 장치인 CPU 간의 현격한 속도 차이 때문에 I/O in the middle of job에 관한 문제점이 있습니다.
2. **Multi-Programming**
    - Batch system에서의 단점을 보완하기 위해 만들어진 기술입니다.
    - 2개 이상의 작업을 동시에 실행하고 운영체제는 여러 개의 작업을 메모리에 동시에 유지, 현재 실행 중인 작업이 I/O를 수행할 경우 다음 작업을 순차적으로 실행합니다.
    - CPU의 쉬는 시간 (idle time)을 최소화 하여 활용율을 극대화 시킬 수 있습니다.
    - 하지만 이런 Multi-Programming에서도 문제점이 있습니다.
        - 우선, 다음 작업의 수행을 위해서는 현재 수행되는 작업이 I/O를 요청하여야 합니다. 즉, 한 명이 의도적으로 I/O를 요청하지 않거나, 마지막에 몰아서 한다면 공평성이 깨집니다.
        - 이러한 누구나 컴퓨터를 많이 사용하고 싶어하는 마음(먼저 업무를 끝내고 싶은건 누구나 같은 마음이겠죠?) 때문에 Multi-programming은 공평성 유지의 필요성이 존재합니다.
        - 이러한 공평성을 위해서 우선 순위를 매겨줘야하며 이는 우선 순위 기반의 실행의 필요성을 보여줍니다.
3. **Time Sharing**
    - Multi programming에서 혼자서 CPU를 차지하는 것을 방지하는 확률을 없애기 위해 등장한 모델입니다.
    - CPU의 실행 시간을 Time slice로 나누어 실행합니다.
    - 모든 프로그램은 정해진 Time slice 동안 CPU를 점유하고 시간이 끝나면 자신의 작업량이 남아있더라도 CPU를 다음 주자에게 양보하여야 합니다.
4. **Multi tasking**
    - Multi-programming 기법에 Time sharing 기법을 더하여 응답성을 올려준 모델입니다.
    - 여러 개의 세부 작업들이 CPU와 같은 자원을 공유하도록 하는 방법입니다.
    - 하나의 작업은 동시에 실행할 수 있는 세부 작업으로 나눠질 수 있습니다.
        
        (예를 들어, 하나의 프로세스에서 여러 개의 자식 프로세스를 생성하는 것과 같겠죠?)
        
    - 사용자가 여러 개의 프로그램 실행이 가능해집니다.
    - 이러한 multi-tasking은 굉장한 효율을 주는 대신에 많은 Issue들이 존재합니다.
        - 적당한 Time slice를 지정해주는 것 ( 너무 자주 일어나면 context-switching의 overhead가 너무 커져요!)
        
        > **context_switching**이란, process가 바뀜에 있어서 일시적으로 정보를 저장하고 다른 실행될 process를 가져오는 process 교환, 즉, 문맥 변경이라 생각하시면 됩니다.
        >
    - 작업 간의 동기화 문제를 해결해줘야 합니다.
    - 복잡한 메모리 관리 시스템이 필요합니다.
        - 동시에 몇 개까지 프로그램을 메모리에 상주시킬 것인가?
        - 메모리 관리 및 보호 시스템이 필요
5. **Others**
    - Multiprocessor systems
        - Symmetric or Asymmetric
    - Embedded systems
        - smart phone, self-driving car
    - 분산 시스템
        - Client-server model, peer-to-peer model
    - Clustered Systems
        - 분산 시스템과 결합하여 Cloud service를 제공합니다.
    - Real-time systems
        - soft real-time, hard real-time
<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>