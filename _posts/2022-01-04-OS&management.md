---
layout: single
title: "01.OS 이해 및 관리"
categories: [System-Security]
tag: [OS, System, Security]
---

## CPU(Central Processing Unit, 중앙처리장치)의 구조적 이해

### 1. CPU 구조 이해

**(1) CPU 개념**

- 입력을 받아 연산하고 그 결과를 출력하는 일련의 과정을 제어 및 조정하는 핵심장치
- 사람으로 치면 두뇌

**[ 구성 요소 ]** 

- ALU(연산장치) : 각종 산술연산들과 논리연산들 수행
- Register(레지스터) : 컴퓨터 장치 중 속도가 가장 빠르며, CPU 내부의 연산 결과를 일싲거으로 기록
    - [레지스터 종류]
        - PC(Program Counter) : 다음 수행 될 명령어가 저장된 주기억장치의 번지 지정
        - MAR(Memory Address Register) : 주기억장치 접근을 위한 주기억장치 번지 기억
        - MBR(Memory Buffer Register) : 주기억장치에 입/출력할 자료를 기억
        - IR(Instruction Register) : 주기억자치에서 인출한 명령어를 기억
- Control Unit(제어장치) : 명령어를 해석, 그것을 실행하기 위한 제어 신호 발생
- 내부 CPU 버스 : ALU와 레지스터 간 데이터 교류를 위한 통로

### 2. 버스 시스템 (Bus System)

**(1) 정의**

버스(Bus)는 시스템에서 데이터들이 다니는 통로이다. 한정된 자원이므로 버스를 이용하기 위해서는 신호를 받아야하며, 이를 사용하는 바식에 따라 입출력 성능에 영향을 준다.

**(2) 버스(Bus)의 종류**

- 데이터 버스(Data Bus) : 시스템 요소 간 처리 데이터를 전송하기 위한 통로
- 주소 버스(Address Bus) : 기억장소의 위치 or 장치 식별을 지정하기 위한 통로 ( 통로의 bit 수에 따라 용량이 결정된다.)
- 제어 버스(Control Bus) : CPU와 기억장치 또는 I/O 장치 사이의 제어 신호를 전송하는 통로

### 3. CPU의 명령 실행 주기(Instruction Cycle)

**(1) Instruction Cycle**

인출(fetch), 간접(indirect), 실행(execution) 및 인터럽트(interrupt)로 구성. 명령어의 실행이 끝난 후, 다음 명령어의 수행 시작과 끝까지의 시간을 뜻 함.

- 인출 (fetch) 단계 : 메모리에서 데이터를 로드(Load)하여 CPU에 있는 레지스터에 적재
- 간접 (indirect) 단계 : 메모리 참조 시 간접 주소 방식 사용하는 경우 실행
- 간접 주소(Indirect Address) 란?
    
    간접 주소란 CPU가 메모리를 참조했을 때 데이터가 존재하는 것이 아니라 메모리에 주소가 존재하여 메모리 내에서 한번 더 메모리를 참조하여 데이터를 얻는 것
    
- 실행 (Execution) 단계 : 명령과 데이터로 CPU가 산술 및 논리연산을 수행하는 것
- 인터럽트(Interrupt) : 컴퓨터 작동 중 예기치 않은 입력이 들어온 경우 비동기적으로 문제를 처리하는 기능을 OS가 제공함. 크게 H/W, S/W 인터럽트로 나뉘어짐.
    - H/W : 기계착오, 외부, I/O, 프로그램 검사 시 발생
    - S/W :  CPU 내부에서 실행한 명령 또는 CPU의 명령 실행에 관련된 모듈이 변화하는 경우 발생
    

## 메모리 시스템(Memory System)

### 1. 기억장치 계층구조(Memory Hierarchy)

**(1) 메모리 계층구조의 이유**

- 액세스 속도 증가 시 비트당 가격도 상승
- 용량이 커질수록 비트당 가격 저하
- 용량이 커질수록 애세스 속도 저하

**(2) 기억장치 계층구조**

**[속도,가격 ↑]** 레지스터 - 캐시 - 주기억장치 - 보조기억장치 **[속도, 가격 ↓]**

### 2. 캐시 메모리(Cache Memory)

**(1) 정의**

CPU와 주기억장치(Memory)의 속도 차이를 극복하기 위해 CPU와 주기억장치 사이에 존재하는 고속의 버퍼 메모리이다. 쉽게 말해, 은행이 본점과 지점이 있다면 본점에 가까이 있지 않은 사람도 지점에 들러 용무를 처리함으로써 본점까지 다녀오는 시간을 줄일 수 있다고 이해하면 될 것 같다.

**(2) 캐시 메모리 Mapping 방식**

1) Direct Mapping (직접 사상)

Main Memory를 여러 구역(Block)으로 분할하여 Cache 슬롯과 *순서대로* 매핑

- 장점
    - 매핑 절차가 단순하고 신속하게 처리
- 단점
    - 높은 ***Cache miss rate*** ( 같은 블록에 사상되는 데이터 캐시 적재 시 교체 발생 )
    - ***Cache miss rate*** 이란?
        
        CPU가 데이터를 이용하려 할 때 해당 자료가 Cache에 적재되지 않은 경우를 뜻 함.
        
- Tag size ?
    
      - 메모리를 2n 개의 구역으로 나눈 경우 n개의 태그가 필요
    
- Cache의 주소(위치) 결정 방법
    1.  메모리 블록 주소 (mod 캐시 전체 블록 수)
    2. 캐시의 블록 수가 2N개일 경우 메모리 주소의 하위 N비트
        
        Index 
        
        | Valid bit | Tag |
        | --- | --- |
        - Index : Cache의 순서 번호(순차적인 주소로 별도의 공간 차지 없음)
        - Valid bit : 저장 데이터의 유효성 bit
        - Tag : 맵핑된 메모리 주소의 Cache 식별을 위한 Index bit로 사용되는 하위 bit를 제외한 상위 bit

2) Associate Mapping(연관 사상)

Main Memory의 각 블록이 Cache의 어느 슬롯이든 적재 가능하다.

- 장점
    - ***지역성(Locality)***이 높은 접근 시 캐시 적중률 높음
        - **지역성(Locality) 란?**
            
             - 지역성이란, 메모리에서 캐시로 적재를 시도할 때 해당 데이터의 주소만 가져오는 것이 아닌 인접한 주소의 데이터도 함께 가져오는 것을 뜻 합니다. 그리하여 해당 지역성이 높은 데이터 접근을 할 경우, Cache miss가 뜰 확률이 적어지는 것 입니다.
            
- 단점
    - 구현 하드웨어가 복잡하여 구현 비용 상승

3) Set Associate Mapping(집합 연관 사상)

Direct Mapping(직접 사상)과 Associative Mapping(연관 사상)에서 보완된 방식으로 캐시와 메모리가 N:1로 매핑되는 방식

- 장점
    - Direct Mapping과 Associative Mapping의 장점 수용
- 단점
    - 캐시 Fin/Fout 발생 증가. 구현 비용이 많이 듦.

### 3. 캐시 메모리 관리 방식

**(1) 정의**

CPU가 요구하는 데이터가 캐시에 없는 경우 이는 Cache miss를 발생시킨다. 이때 Cache는 주기억장치에서 해당 데이터를 다시 가져와야한다. Cache memory 관리란 이렇게 CPU가 데이터를 요구하는 경우 해당 데이터가 Cache에 상주할 수 있도록 하는 것을 의미한다.

**[인출 방식]**

- Demand Fetch : 필요 시 캐시를 인출
- Pre-Fetch : 예상되는 블록을 미리 패치 해 두는 방식

**(2) 캐시 메모리 교체(Replacement) 알고리즘의 종류**

1) FIFO(First In First Out)

 - 순서대로 나간다고 생각하면 된다. 맨 처음 들어온 것이 가장 먼저 나가는 개념

2) LFU(Least Frequently Used)

 - 뜻 그대로 가장  적게 사용된 Page부터 교체되는 것이다. 이는 최근 적재된 Page가 교체될 가능성이 있다.

3) LRU(Least Recently Used)

 - 가장 오랫동안 사용되지 않은 Page를 교체하는 방법. 해당 방식을 사용하기 위해서는 시간을 기록하여야 하며, 이는 Overhead를 발생시킨다.

4) Optimal(최적)

 - 향후 가장 참조되지 않을 Page를 교체하는것

 - 단, 해당 방안은 향후 Page의 참조 여부를 파악해야 하므로 실현이 불가능

5) NUR(Not Used Recently)

 - 참조 비트와 수정 비트를 사용하여 최근 사용되지 않은 Page 교체

6) SCR(Second chance Replacement)

 - 시작할 때 참조 비트는 ‘1’로 set하고 이후 일정 시간이 지나면 ‘0’으로 set. 

 - 자신이 교체되어야 할 순간에 참조 비트가 ‘1’인 경우 0으로 변경하고 한번 더 기회를 줌. 

 - 0인 경우는 바로 교체.

※ ***Thrashing*** 발생에 주의하여야 한다.

- **Thrashing 이란?**
    
     - Thrashing 이란, Page Fault의 빈도가 높은 경우 CPU가 Process 수행보다 Page 교체에 더 많은 시간을 소비하는 비정상적인 현상을 뜻 합니다.
    
- **해결 방안**
    1. Load Control : 일정 시간 동안 새로운 프로세서가 새서되는 것을 지연 → Suspend Queue에 대기시켜서 Thrashing 현상을 감소
    2. Locality(지역성) : 시간과 공간 지역성을 집중적으로 참조하여 Page Fault 빈도를 낮춤
    3. Working Set : 일정 시간 동안 참조되는 페이지 집합을 주기억장치에 유지
    4. Page Fault Frequency(PFF) : Process의 Page Fault 빈도에 따라 Residence set 조정(PFF와 Residence set은 비례 관계)

### 4. 캐시 메모리 일관성(Cache Coherence)

**(1) Write-through 방식**

- Write-through 방식이란, Multi-processor 환경에서 processor가 각각의 cache memory를 가질 때 해당 cache에서의 data와 Main memory의 data를 동시에 동기화 해주는 방식을 뜻 한다. 이때, 다른 Processor와 값을 변경한 Processor와의 data 차이가 발생할 수 있으며, 동시에 Main memory의 data와도 차이가 날 수 있다. 이러한 data의 차이를 일관성이 깨졌다고 표현한다.

**(2) Write-Back 방식**

- Write-Back 방식은 Multi-processor 환경에서 각 processor가 cache memory를 소지할 때, 해당 cache에서 data의 값이 변경될 경우 자신의 cache memory만 우선적으로 값을 변경하고 이후 따로 Main memory에 값을 저장하도록 처리하는 방식을 말한다. 이 경우, 값을 변경한 processor의 cache memory의 data를 Main memory에 기록하기 전까지 다른 processor의 cache memory와 main memory는 해당 processor의 data와 다른 값을 가져 일관성이 깨지는 상황이 초래된다.

### 5. 가상 메모리 시스템

**(1) 정의**

- ‘가상 메모리(Virtual memory)’란, 기존에 존재하던 Main memory의 용량 문제로 인해 생겨난 Mechanism으로 Secondary Memory Unit(보조기억장치)를 Main memory처럼 사용하여 공간을 확대하는 관리 방법이다.

**(2) 단위**

- 가상 메모리에는 두 가지의 memory 관리 단위가 있다. - Page, Segment
- Page : Page는 가상 메모리를 동일한 크기의 최소 논리 분할 단위로 나눈 것을 뜻한다.
- Segment : Segment는 가상 메모리를 용도별로 가변적인 크기로 분할한 것을 뜻한다.

**(3) Page의 특징** 

1. 분할되는 크기가 고정적이다.
2. 논리주소는 Page 번호와 Offset으로 구성된다.
3. 요구 Page만 Main memory에 일부 적재가 가능하다.
4. 메모리 관리 측면에서 생겨난 기법이다.
5. 해당 Page만 교체하면 되기 때문에 교체 시 소모되는 시간이 적고, 외부 단편화(External Fragmentation) 문제를 해결할 수 있다.
6. External Fragmentation 문제를 해결했으나 고정적 크기의 블록이기 때문에 마지막 조각은 내부 단편화(Internal Fragmentation)를 발생 시킬 수 있다.
7. 자주 교체되는 상황에서는 교체 시간의 합으로 인해 오히려 효율이 떨어질 수 있는데 이러한 현상을 ‘Thrashing’이라고 칭한다.

**(4) Segment의 특징** 

1. 분할되는 크기가 data에 따라 가변적이다.
2. 논리주소는 Segment 번호와 Offset으로 구성된다.
3. 한 번에 해당되는 program이 전체 적재된다.
4. 파일 관리 측면에서 고안한 기법이다.
5. 전체적으로 구현이 쉽다는 장점이 있고, 프로그램 전체가 그대로 옮겨지기 때문에 Internal Fragmentation이 발생하지 않습니다.
6. 그러나, 가변적인 크기와 프로그램의 전체 적재는 심각한 External Fragmentation을 유발할 수 있습니다.
7. 또한 한 번에 프로그램 전체를 적재시키기 때문에 사용되는 Main memory의 크기가 커야한다는 요소도 존재합니다.

## I/O Interface

**(1) 입출력 처리에 대한 변화**

1) Program에 의한 I/O 처리

- Program에 의한 I/O 처리는 CPU가 연산 도중 I/O가 발생하면 보조기억장치에서 데이터를 읽어와 주기억장치에 적재하고 CPU는 주기억장치를 참조해서 데이터를 읽어오는 방식으로, CPU는 I/O event를 처리하는 동안 다른 작업을 수행할 수 없다는 문제점이 존재.

2) Interrupt에 의한 I/O 처리

- Interrupt란, 비동기적 event에 관한 처리를 위한 방식이며 I/O event가 발생했을 때 이를 식별하고, Interrupt 처리 루틴에 의해 해당 event를 처리하는 방식이다. 이 경우, Program에 의한 I/O 처리보단 CPU 관여가 적지만, 그래도 CPU는 입출력을 대기해야 하는 문제가 발생한다.

3) DMA

- Interrupt나 Program에 의한 처리와는 다르게 새로운 장비를 사용하는 것으로 해당 장비의 이름이 DMA(Direct Memory Access) 이다. 이는 처음에 CPU에게 제어 정보를 받고 난 다음은 CPU의 개입 없이 I/O 장치와 기억장치 사이의 data를 전송하는 장치로 효율적이다.

4) I/O Processor

- 독립적인 장치로 현재 대부분의 컴퓨터에서 사용되고 있다. 독립적으로 입출력을 수행하며 고속으로 데이터를 전송할 수 있다는 장점이 있다.
- 크게 Selector 채널과 Multiplexer 채널로 나뉘어지며 Selector 채널은 한 번에 한 개씩 데이터를 전송하고 Multiplexer는 한 번에 여러개의 데이터를 전송할 수 있다. 이러한 Multiplexer는 Byte Multiplexer와 Block Multiplexer로 나뉘어진다.
