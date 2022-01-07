---
layout: single
title: "CPU Scheduling #2"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
CPU Scheduling 기법 중 두 번째 section을 학습한 내용입니다.

# Multiple processor scheduling

→ 여러 개의 CPU를 사용함에 있어 훨씬 복잡한 scheduling이 요구됩니다.

- 종류의 관점
    - heterogeneous processors - 다른 종류들로 이뤄진 상태를 말합니다.
    - homogeneous processors - 같은 종류들로 이뤄진 상태를 말합니다.

- 방식의 관점
    - Asymmetric multiprocessing
        - 하나의 프로세서가 System data structure에 접근이 가능합니다.
    - Symmetric multiprocessing(SMP)
        - 오늘 날 많이 사용되어집니다.
        - 각각의 프로세서들이 하나의 동일한 메모리, 자료구조를 이용하는 방식입니다.
        

### Processor affinity ( 프로세서 관련성 )

→ Process는 processor에 대해 관련성을 가집니다. 

- Soft affinity
    - 하나의 프로세스가 항상 같은 프로세서에게서 동작하기로 보장되어 있는 것이 아닌 변동의 가능성이 있습니다.
    - ***Load balancing***에 대한 장점이 존재합니다.
    - Context swiching에 따른 Overhead가 있다는 점이 있습니다.
- Hard affinity
    - Soft affinity와 반대로 항상 같은 프로세서에서 동작됩니다.
    
    > ***Load balancing이란?***
    > 
    > 
    > 
    > → 말 그대로 작업량을 고르게 분포시키는 것을 뜻합니다. SMP 방식이라면, 효율성을 위해 모든 CPU를 로드한 상태로 유지해야 하고, 여기에 있어 workload가 고르게 분산되도록 Load balancing을 시도하게 됩니다. 
    > 
    > Load Balancing에는 두 가지 방식이 존재합니다.
    > 
    > - Push migration
    >     - 바쁜 cpu가 있는지 체크하고 그 경우 해당 cpu의 task를 다른 널널한 cpu로 밀어 보냅니다.
    > - Pull migration
    >     - 쉬고 있는 cpu가 바쁜 cpu의 waiting 상태의 작업을 당겨와 자신이 처리합니다.

# Multicore processors

→ 우리가 사용하는 휴대용 device들은 크기가 작아야하기 때문에 여러개의 processor가 사용되는데 제한됩니다. 그러므로, 하나의 CPU에 여러 개의 core를 사용하여 부피를 감소시키는 방법을 사용합니다. 이는빠르고 적은 전력을 소비하는 장점이 있습니다.

## 구조

- 하나의 프로세스는 여러 개의 core로 이루어집니다.
- 그리고 각 core는 여러 개의 Thread로 구성됩니다. 여기서 사용되어지는 Thread는 OS 개념에서의 Thread가 아닌 Hardware 개념의 thread로 SMT(Simultaneous multithreading)라고 불립니다. 이러한 SMT는 ***memory stall***이 일어나는 동안 다른 Thread가 작업(이전과 다른 작업)을 처리하여 효율을 증가시켜줍니다.
    
    > Memory stall → Main memory에서 주소를 가져오는 것
    > 

## Multicore programming

### 고려사항

→ Multi processor 시스템엔 다음과 같은 고려해야할 사항들이 존재합니다.

1. Dividing Activities ( 작업 분할 )
2. Balance
3. Data Splitting ( 공유 데이터 분할 )
4. Data Dependency
5. Testing & Debugging

### Parallelism (병렬처리)

- 동시에 하나 이상의 작업을 처리하는 것을 말합니다.
- Multi-core system에서 구현 가능합니다.

### Task Parallelism

→ 하나의 작업을 여러개의 스레드가 처리하는 방법입니다.

### Data Parallelism

→ 하나의 데이터를 여러개가 사용하는 방법입니다.

![▲ Parallelism on a multi-core system](/images/multicorethread.png)

▲ Parallelism on a multi-core system

### Concurrency (동시처리)

- Single processor / core, scheduler 환경에서 하나 이상의 작업을 수행하는 것을 말합니다.
- 작업이 매우 빠르게 교체되며 처리되기 때문에 마치 동시에 처리하는 것 같아 Concurrency라는 용어를 사용한 것이지 실제로 동시에 일을 처리하는 병렬처리와는 다소 차이가 존재합니다.

![▲ Concurrent execution on single-core system](/images/singlecorethreadpng)

▲ Concurrent execution on single-core system

### Amdal’s Law

→ 그렇다면 우리는 과연 무작정 Core의 개수를 늘리면 컴퓨터의 성능은 좋아지지 않을까? 라는 생각을 하게 됩니다. 하지만 그러한 궁금증은 Amdal’s Law로 해결이 가능합니다.

![▲ Amdal’s Law](/images/amdalslaw.png)

▲ Amdal’s Law

여기서 serial portion이란 순차적으로 처리해야할 작업의 portion(비율)을 의미합니다. 즉 Core의 개수를 아무리 늘려도 Parallelism이 가능한 비율, 즉 (1-S)가 커지지 않는다면 선형적인 속력증가는 기대하기가 힘들다는 것을 알 수 있습니다.

## Process vs Threads

- **Process**
    - 프로그램 차원에서 생기는 하나의 domain입니다.
    - 독립적인 주소 공간을 소유하고, 각 프로세스들은 서로간 침범이 불가능합니다.
- **Threads**
    - 하나의 프로세스 안에 하나의 code와 section을 공유하는 여러 개의 실행 흐름을 뜻합니다.
    - Thread switch는 context switch와 달리 주소 공간에 변화가 없으므로 overhead가 들지 않습니다.
    - 다음과 같은 이점들을 챙길 수 있습니다.
        - Responsiveness - 응답성, Interrupt 발생 시에도 일을 처리하며 빠르게 처리 가능합니다. 일반적으로 UI 처리에 용이합니다.
        - Resource Sharing - Memory 자원을 공유하기 때문에 효율적인 동작이 가능합니다.
        - Performance - 위와 같은 이유들로 수행 능력이 향상됩니다.
        - Scalability - 프로세스는 multiprocessor 구조의 이점을 챙길 수 있습니다.
        

## Realtime Scheduling

→ 실시간 응답성을 중요하게 여깁니다.

### Soft-realtime

→ Deadline이 따로 정해지진 않습니다.

### Hard-realtime

→ Deadline이 존재하고 이를 어길 시 생명 등 중요한 것들이 피해를 볼 수 있습니다.

### Type of Latency

→ 2가지 타입의 지연이 생길 수 있습니다.

- Interrupt latency
    - 말 그대로 인터럽트 처리로 인한 지연입니다.
- Dispatch latency
    - 현재있는 process가 종료되었을 때 다른 process로 교체하기 위해 schedule하는데 걸리는 지연 시간을 의미합니다.

    
<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>