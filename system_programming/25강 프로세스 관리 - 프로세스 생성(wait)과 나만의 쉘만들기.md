# 제 25강 프로세스 관리 - 프로세스 생성(wait)과 나만의 쉘만들기
## fork()와 exec()
- 리눅스 프로세스 실행 
  - 부모 프로세스로부터 새로운 프로세스 공간을 만들고 부모 프로세스 데이터 복사(fork)
  - 새로운 프로세스를 위한 바이너리를 새로운 프로세스 공간에 덮어씌움(exec)

---
## wait() 시스템콜 
- wait() 함수를 사용하면, fork() 함수 호출시 자식 프로세스가 종료할때까지 부모 프로세스가 기다림 
- 자식 프로세스와 부모 프로세스의 동기화, 부모 프로세스가 자식 프로세스보다 먼저 죽는 경우를 막기 위해 사용(고아 프로세스 혹은 좀비 프로세스)

![wait systemcall](../img/wait_systemcall.png)

---
## fork(), exec(), wait() 시스템콜
```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/wait.h>
#include <sys/types.h>

int main() {
    int pid;
    int child_pid;
    int status;

    pid = fork(); // 새로운 프로세스 공간(based on 부모 프로세스)
    switch(pid) {
        case -1: 
            perror("fork is failed\n");
            break;
        case 0: // 자식 프로세스
            // ls 바이너리로 덮어씌움
            execl("/bin/ls", "ls", "-al", NULL);
            perror("execl is failed");
            break;
        default: // 부모 프로세스
            child_pid = wait(NULL); // 자식이 끝날때까지 기다림
            printf("ls is complete\n");
            printf("Parent PID(%d), Child PID(%d)\n", getpid(), child_pid);
            exit(0);        
    }
}

```

- execl()만 사용하면 부모 프로세스가 사라짐
- 이를 유지하기 위해, fork()로 새로운 프로세스 공간 복사 후, execl() 사용 
- wait() 함수를 사용해서 부모 프로세스가 자식 프로세스가 끝날 때까지 기다릴 수 있음 

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>
#include <sys/types.h>
#define MAXLINE 64

int main(int argc, char **argv) {
    char buf[MAXLINE];
    pid_t pid;
    printf("ZorbaShell ver 1.0\n");
    while(1) {
        memset(buf, 0x00, MAXLINE);
        // stdin(표준입출력을 말하는데, 키보드 입출력), fgets로 63bytes만큼 메모리를 가져오겠다는 뜻
		fgets(buf, MAXLINE - 1, stdin); 
        // char *fgets (char *string, int n, FILE *stream)
		if(strncmp(buf, "exit\n", 5) == 0) {
			break;
		}
		buf[strlen(buf) - 1] = 0x00;

		pid = fork(); 
		if(pid==0) {
			if(execl(buf, buf, NULL) == -1) {
				printf("command execution is failed\n");
				exit(0);	
			}
		}
		if(pid > 0) {
			wait(NULL);
		}
	}
	return 0;
}
```

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/wait.h>
#include <sys/types.h>
#define MAXLINE 64

int main(int argc, char **argv) {
    char buf[MAXLINE];
    pid_t pid;
    printf("ZorbaShell ver 2.0\n");
    while(1) {
        memset(buf, 0x00, MAXLINE);
        // stdin(표준입출력을 말하는데, 키보드 입출력), fgets로 63bytes만큼 메모리를 가져오겠다는 뜻
		fgets(buf, MAXLINE - 1, stdin); 
        // char *fgets (char *string, int n, FILE *stream)
		if(strncmp(buf, "exit\n", 5) == 0) {
			break;
		}
		buf[strlen(buf) - 1] = 0x00;

		pid = fork(); 
		if(pid==0) {
        // 환경변수 없이 파일이름으로만 실행함 -> execlp()   
			if(execlp(buf, buf, NULL) == -1) {
				printf("command execution is failed\n");
				exit(0);	
			}
		}
		if(pid > 0) {
			wait(NULL);
		}
	}
	return 0;
}
```