---
layout: single
title: "Synchronization"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
동기화에 대해 학습한 내용입니다.

# 동기화 문제

## 개요

→ 프로세스들 간에 공유되는 data가 생기면서 그 data에 대한 정확성 및 Consistency(일관성)가 보장되어야 합니다.

### Race Condition

→ 공유된 Data의 Consistency가 보장되지 못한 예로, 하나의 data에 동시에 접근하여 값을 바꾸려하면 기대했던 값, 즉, 정확한 값이 측정되지 않은 상태를 일컫는 용어입니다. 이때 우리는 해당 공유 Data의 값을 변경하는 구간을 ‘Critical Section’이라고 합니다.

### 필요성

- Thread programming → data section is shared
- Shared Memory → Multiple proceesses can access
- Kernel and structures

## Critical Section

### 정의

→ 하나의 System에서 여러개의 Processes이 존재할 경우 각 Process는 Segment of code를 가집니다. 이를 Critical section이라고 하며, 쉽게 말하자면 한 개이상의 Process가 공유하는 data에 접근하고 값을 변경하는 code를 말합니다. 

### Entry Section

→ 각 프로세스들은 critical section 진입을 위해 승인을 받아야합니다. Entry section은 해당 승인을 대기하는 코드 부분입니다.

### Exit Section

→ 각 프로세스들이 Entry Section을 끝마치고 나온 코드 부분입니다.

### Remainder Section

→ 이후 프로세스들의 나머지 부분을 처리하는 코드입니다.

```c
// 다음은 수도 코드입니다.
while(true) {

	// Entry Section
			// Critical Section
	// Exit Section
			// Remainder Section
```

### 충족되어야 할 3 요소

1. Mutual Exclusion : 하나의 프로세스가 Critical Section에 있으면 다른 프로세스는 접근이 불가능하다는 성질입니다.
2. Progress : Critical Section 내에 작동 중인 Process가 없고 들어가길 희망하는 Process가 있다면 유한 시간 내에 들어가서 작업을 수행할 수 있어야 합니다.
3. Bounded waiting : 요청을 한 후 유한 시간 내에 Critical section에 진입할 수 있어야합니다. 
    
    ⇒ 예를 들어, Process 하나가 Critical Section 내에서 무한 loop를 돌고 있는 상태라면 Progress는 보장되었지만, Bounded waiting은 보장되지 못했다고 말할 수 있습니다.
    

### Peterson’s Solution

→ Peterson의 방법에서는 공유되는 두 가지 data가 존재합니다.

- int turn;
- boolean flag[2];

```c
while(true){
// Entry section
	flag[i]=true;
	turn = j;
	while (flag[j] == true && turn == j)
			// waiting 
	// Critical Section
	flag[i] = false; // Exit section
		// remainder section
}
```

→ Peterson’s solution은 3요소를 모두 충족시킵니다.

※ 단, 두 개의 프로세스에  한해서만 만족됩니다. 그 이상부터는 구현이 매우 복잡해집니다.

### Hardware Instructions

→ 사람들은 초기 값을 지정할 때 서로에게 간섭이 없으면 어떨까? 라는 생각을 하게 되고 이는 Hardware 명령어의 특징인 원자적 특징을 이용하여 해결하려 했습니다. 원자적이란, 더이상 쪼갤 수 없는 입자를 ‘원자(atom)’이라고 하는데 이 특징을 말하는 것 입니다. 해당 명령어를 진행 중 다른 Process가 interrupt를 할 수 없기 때문에 이를 ‘atomical하다’라고 말합니다. 

(1) test_and_set(int *value)

```c
do {
	while(test_and_set(&lock)) // 자물쇠를 잠금
	; // do nothing
	// Critical section
	lock = false;              // 자물쇠 해제
		// remainder section
} while(true);
```

(2) compare_and_swap(int *value, int expected, int new_value)

```c
int compare_and_swap(int *value, int expected, int new_value)
{
	int temp = *value;
	if(*value == expected)
			*value = new_value;
	return temp;
}

while(true) {
	while(compare_and_swap(&lock, 0, 1) != 0) // 자물쇠를 잠금
		; // do nothing
	// Critical section
	lock = 0;                                 // 자물쇠 해제
			// remainder section
}
```

- Compare_and_swap의 Bounded waiting

[초기값]

→ boolean waiting[n] = {false};

    lock = 0;

```c
while (true) {
	waiting[i] = true;
	key = 1;
	while (waiting[i] && key == 1) // 한 번은 무조건 in
		key = compare_and_swap(&lock, 0, 1); // lock이 0인 경우 -> 1이 되면서 key가 0이 됨. => while문 탈출
																				 // lock이 1인 경우 -> 0이 되면서 key가 1이 됨. => lock 상태가 0이 될 때까지 대기
	waiting[i] = false;
		// Critical Section
	j = (i + 1) % n; // i의 다음 순번
	while((j!=i) && !waiting[j]) // 대기중인 waiting[j] 찾는 중
		j = (j + 1) % n;
	if(j==i) // 대기중인 j가 없는 경우
		lock = 0; // 잠금 해제
	else // 대기중인 j 존재
		waiting[j] = false;
	
	// remainder section
}
```

⇒ 이렇듯 bounded waiting은 mutual exclusion처럼 동기화 Instruction을 사용한다고 해서 무조건 만족하는 것이 아닌 사용자 프로그램에서 제공해야하는 속성입니다. 그러나, 주어진 문제마다 차이가 있기 때문에 완벽한 처리를 기대하기는 힘들다는 점이 있습니다.

## Mutex Locks

→ Software level에서도 critical section problem을 해결할 수 있습니다. 그것을 도와주는 도구가 바로 ‘mutex locks’ 입니다. 여기서 mutex는 ‘Mutual Exclusion’의 약어로 ‘mut’와 ‘ex’를 합한 단어입니다.

- 사용자는 critical section에 들어가기 전 lock을 걸고 나올 때는 lock을 해제하여야 합니다. 방문을 잠그는 표현이라고 생각하시면 될 것 같습니다.
- Mutex에서 lock은 boolean 변수(1과 0으로 표현 가능)입니다.

코드로 예시를 들겠습니다.

```c
while (true) {
	acquire(&lock)
		// critical section
	release(&lock)
		// remainder section
}

void acquire(int *available) {
	while (!available) // 사용할 수 없으면 계속 loop를 돌면서 대기
		; // busy waiting -> CPU 소비
	available = 0; // false
}

void release(int *available) {
	available = 1; // 사용을 마쳤으면 다시 자원을 돌려놓음
}
```

## Semaphore(S)

→ 하나의 정수 변수입니다. 선언 부분을 제외하고는 wait(), signal()로만 접근이 가능합니다.

S를 자원이라고 생각하시면 편합니다.

```c
void wait(int *S) { // entry section에서 사용
	while(*S<=0) // 자원이 없는 경우 대기
	; // busy wait
	*S--; // 자원이 있으면 사용
}

void signal(int *S){ // exit section에서 사용
	*S++; // 자원 반납
}
```

- Type of Semaphore
    - Counting semaphore → semaphore의 value가 제한적이지 않고, 초기값은 제공 가능한 자원의 수로 적용됩니다.
    - Binary semaphore → semaphore의 value가 ‘0’과 ‘1’만 가능, 구현이 쉽습니다. Binary semaphore를 이용해서 counting semaphore의 구현이 가능합니다.
- Busy waiting의 해결책 → waiting queue!
    - 원래는 busy waiting을 통해 대기를 하던 애들의 상태를 waiting state로 바꾸고 queue에 대기 시키는 방법을 말합니다.
    - 이때, CPU scheduler가 다음 실행할 process를 정합니다. 이 process는 기존의 critical section을 이용하던 process가 나오면서 보낸 signal로 자원이 생겼는지를 판단하고 critical section에 진입합니다.
    
    ```c
    typedef struct{
    	int value;
    	struct process *list;
    } semaphore;
    
    void wait(semaphore *S) {
    	S->value--;
    	if(S->value<0) {
    		// add this process to S->list; : waiting queue로 보내는 행위
    		sleep();
    	}
    }
    
    void signal(semaphore *S) {
    	S->value++;
    	if(S->value<=0) { // 아직 대기중인 P가 존재
    		// remove a process P from S->list;
    		wakeup(p);
    	}
    }
    ```
    

### Binary Semaphore → no sleep queue

- Test_and_set 이용
- while((test_and_set(&S)) // 현재 S의 값(’0’)이 리턴되고 S는 1이 됩니다.
- V(S) → S를 0으로 돌려놓습니다.

### Implemetation : Counting Semaphore by using Binary semaphore

- Initialization
    
    ```c
    // 수도 코드 입니다.
    Semaphore initially K {
    	integer val = K; // value of S
    	Binary semaphore wait = 0; // wait here to wait on S
    	Binary semphore mutext = 1; // protects val
    }
    ```
    
- wait operation
    
    ```c
    P(S){ //wait
    	P(S.mutex);
    	if(S.val <= 0){
    		S.val--;
    		V(S.mutex);
    		P(S.wait);
    	}
    	else {
    		S.val--;
    		V(S.mutex);
    	}
    }
    ```
    
- signal operation
    
    ```c
    V(S) { //signal
    	P(S.mutex);
    	if(S.val < 0) {
    		V(S.wait);
    	}
    	S.val = S.val + 1;
    	V(S.mutex);
    }
    ```
    
    ⇒ 이러한 Semaphore에도 P()를 critical section 앞 뒤로 쓰거나, P()와 V()의 순서를 바꾸어 쓰는 실수 등 한계점이 존재합니다. 이러한 문제 때문에 High-level에서 동기화를 제공하는 방법이 필요했는데 이러한 고민으로 인해 ‘Monitor’이 생겼습니다.
    

## Monitor

→ 여기서 말하는 Monitor은 컴퓨터 화면을 나타내는 Monitor가 아닙니다! 하나의 추상적인 데이터 타입입니다.

- Application은 procedure를 호출하는 것만으로 동기화를 해결할 수 있습니다. 이로 인해, programmer들은 동기화(P, V)를 명시적으로 코딩할 필요가 사라집니다.
- “synchronized” 블럭을 통해 동기화되는 영역을 지정 가능해집니다.
    
    ```cpp
    public class account{
    	private int balance;
    	public synchronized int increaseBalance(int amount){ // critical section
    		return balance += amount;
    	}
    }
    ```

<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>