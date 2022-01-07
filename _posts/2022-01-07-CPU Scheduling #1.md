---
layout: single
title: "CPU Scheduling #1"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
CPU Scheduling 기법 중 첫 번째 section을 학습한 내용입니다.

# Process scheduling

## Process scheduling 이란?

→ CPU 사용 효율을 높이기 위해, 프로세스에게 자원을 배분할 순서를 정하는 것을 일컫는 말입니다.

## CPU & I/O bursts

- Burst : 처리해야할 데이터가 모이면 한번에 처리하는 것을 말합니다.
- Process 처리 과정에서 CPU와 I/O 처리를 해줘야합니다. (즉, Process 실행은 CPU 실행과 I/O 대기로 이뤄져 있습니다. 즉, CPU burst가 일어나야 I/O burst가 일어날 수 있습니다.)
- CPU 처리 → CPU burst, I/O 처리 → I/O burst
    
    ※ I/O b urst 동안 CPU는 idle 상태가 되는데 이때 CPU를 어떻게 사용하냐에 따라 효율에 영향을 미칩니다.
    

### CPU bound와 I/O bound

- CPU bound process
    
    → CPU 작업이 I/O 작업보다 비중이 큰 경우
    
- I/O bound process
    
    → I/O 작업이 CPU 작업보다 비중이 큰 경우
    

## Schedulers

### Short-term Scheduler(or CPU schduler)

- 짧은 시간 단위의 처리
- 예를 들어, 다음 CPU 배정을 할 Process를 빠르게 선택해줘야 할 경우 사용되는 Scheduler입니다.

### Long-term Scheduler(or Job scheduler)

- ready queue에 process를 무엇을 가져올지 정하는 것
- CPU scheduler에 비해 비교적 term이 깁니다.

### Medium-term Scheduler

- CPU에 진입하면 모두 Main Mem에 적재되어 가져오는 크기의 한계를 해결한 Scheduler 입니다.

## Short-term Scheduler(상세)

→ ‘CPU Scheduler’라고도 불리우며, ready queue에 대기 중인 하나의 Process에게 CPU를 할당해주는 Scheduler입니다.

### CPU scheduling이 일어나는 경우

- running state → wating state
- running state → ready state
    - 이를 고려한 것을 선점형 ( Preemptive ), 고려하지 않은 것을 비선점형( Non-preemptive ) 라고 합니다.
- waiting state → ready state
- Terminate

### Non-preemptive & Preemptive Schedulers

- **Non-preemptive**
    - 프로세스가 작업을 수행하는 중 발생하는 프로세스 간 인터럽트를 허용하지 않습니다.
    - 이로 인해, 응답성 저하와 Overwirte 가능성으로 인한 데이터 유실 염려오 인해 대부분 Preemptive를 사용합니다.
    
- **Preemptive**
    - OS가 실행 중인 프로세스를 제어하거나 인터럽트를 할 수 있습니다. ( 즉, 프로세스간 인터럽트가 가능합니다. )
    - 단, 동기화 사건( Synchronization issue) 발생이 가능하고 공유 데이터 고려, 커널 모드에서 선점 고려와 OS 처리 중 인터럽트를 고려 등 고려할 것이 많기 때문에 구현이 복잡합니다.

### Scheduling criteria

→ scheduling 기준

- **CPU utilization**
    
    → CPU 효율을 최대로 하는 것을 추구합니다. 
    
    → 높은 수치일수록 좋습니다.
    
- **Throughput (작업량)**
    
    → 정해진 시간동안 몇개의 프로세스가 작업을 완료했는지를 고려합니다.
    
    → 높은 수치일수록 좋습니다.
    
- **Turnaround time**
    
    → 작업완료(process 생성 → 종료)까지 걸리는 시간을 의미합니다.
    
    → 시간이 짧을수록 좋습니다.
    
- **Waiting time**
    
    → 프로세스가 ready queue에서 기다리는 시간을 의미합니다.
    
    → 시간이 짧을수록 좋습니다.
    
- **Response time**
    
    → 응답시간을 말하며, 사용자가 요청한 작업을 처리하는 시간을 의미합니다. 프로세스보다 작은 작업단위의 처리시간을 나타내는 것으로, 예를 들어 키보드에서 입력이 발생한 경우 화면 출력까지의 소요 시간을 의미합니다.  쉽게 말해보자면, Process가 실행 도중 요청하는 세부 요청들에 대한 처리시간을 의미합니다.
    
    → 시간이 짧을수록 좋습니다.
    

***⇒ 모든 criteria(기준)을 만족할 수는 없습니다. 특정한 목적에 맞는 필요한 criteria를 충족시키는 것이 중요합니다***

## Scheduling Algorithms

### First Come, First Served ( FCFS )

- First In First Out과 같은 원리입니다.
- Non-preemptive algorithm입니다.
- CPU가 ready queue에서 프로세스들을 가져온 순서대로 처리를 합니다. 이로 인해, 먼저 도착한 프로세스들의 waiting time에 따라 평균 waiting time이 크게 달라질 수 있습니다.
- ***Convoy Effect***가 발생할 가능성이 있습니다.
    
    > ***Convoy Effect란?***
    > 
    > 
    > 
    > → CPU burst time이 작은 프로세스가 큰 프로세스보다 ready queue의 뒤에 위치하여 전체적인 시스템 효율이 저하되는 현상
    > 
    > → 예를 들어보면, 마트에서 껌하나만 살 소비자와 여러 개의 품목을 사려고 하는 소비자가 있다면 껌을 산 소비자는 잠시만 계산을 하면 되는데, 앞에 있는  소비자 때문에 비효율적인 시간을 소요하여야 하는 상황에 처해집니다. 이러한 경우를 말하는 용어가 ***‘Convoy Effect’*** 입니다.
    > 

### Shortest-Job-First ( SJF )

- Non-preemptive algorithm입니다.
- Burst-time이 짧은 것부터 처리하는 방식을 말합니다. 하지만 모든 프로세스들의 burst-time을 미리 알고있어야 한다는 점에 있어서 제한적일 수 있습니다.
- ***Priority Scheduling*** 기법 중 하나입니다.
    
    > ***Priority Scheduling 이란?***
    > 
    > 
    > 
    > → 우선순위를 기반하여 scheduling하는 기법을 의미합니다. 여기서 우선순위라 하면, burst time을 의미합니다. 이러한 우선순위 기반의 채택 기법은 낮은 우선순위들은 계속하여 자신보다 burst time이 짧은 프로세스가 ready queue에 존재할 경우 계속해서 자원을 받지 못하는 ***‘Starvation(기아현상)’***에 빠질 수 있습니다. 해당 기아현상을 해결하는 방법 중 하나는 ***‘Aging’***이라는 기법이 있습니다. ‘Aging’은 ready queue에 waiting하는 시간에 비례하여 priority를 올려주는 것을 의미합니다.
    > 

### Shortest-remaining-time-first

- Preemptive algorithm 입니다.
- 프로세스가 ready queue에 도착했을 때 전체 프로세스들의 남은 burst-time을 계산하여 짧게 남은 것부터 처리하는 방식입니다.
- 프로세스를 처리하다가도 burst time이 낮은 것이 도착하면 context switching을 진행합니다.

### Round-Robbing (RR)

- Time-sharing을 기반한 Preemptive algorithm입니다.
- 자신에게 주어진 시간을 의미하는 ***‘Time Slice’*** 안에 처리를 완료하지 못하면 ready queue의 끝으로 이동하여 다음 순서를 기다립니다.
- Time slice가 너무 짧으면 잦아진 Context switching 때문에 과도한 Overhead를 유발할 수 있으며, 반대로 너무 길면 FCFS 방법과 다를 바가 없습니다.

## 사용하는 Queue에 따른 Priority Scheduling

### Single Queue

- 대표적인 단일 queue을 사용하는 Priority Scheduling 중에는 SJF가 있습니다.
- 하나의 ready queue만을 사용합니다.

### Multi-level Queue

- 각 ready queue마다 priority가 있으며, 그 queue안에서도 프로세스를 schedule 할 기법이 정의되어있어야 합니다.
- 단, 1번 queue가 2번보다 우선순위가 높을 경우 1번 queue의 프로세스가 먼저 schedule되어 전부 끝이나면 2번 queue를 진행하는데 이때 ‘starvation’ 가능성이 있습니다.

### Multi-level feedback queue

- Multi-level queue에서의 starvation 현상을 수정한 모델입니다.
- 프로세스가 aging 기법을 도입하여 queue들 사이에서 이동이 가능합니다.
- 이를 처리하기 위한 5가지 설정이 존재합니다.
    - number of queues
    - scheduling algorithms for each queue
    - process priority upgrade에 대한 기준 정의
    - process priority demote에 대한 기준 정의
    - process가 서비스를 필요로 할 때 들어갈 queue 지정

<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>