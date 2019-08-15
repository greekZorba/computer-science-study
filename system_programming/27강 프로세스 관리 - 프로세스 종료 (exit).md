# 제 27강 프로세스 관리 - 프로세스 종료 (exit)
## 프로세스 종료
- exit() 시스템콜: 프로세스 종료
```c
#include <stdlib.h>
void exit(int status); // status에 0을 넣는건 정상종료를 의미
```
- main 함수의 return 0;와 exit(0);의 차이는?
  - exit() 함수: 즉시 프로세스를 종료함(exit() 함수 다음에 있는 코드는 실행되지 않음)
  - return 0: 단지 main()이라는 함수를 종료함 
    - 단, main()에서 return 시, C 언어 실행 파일에 기본으로 포함된 _start() 함수를 호출하게 되고, 해당 함수는 결국 exit() 함수를 호출함 
    - _start() 함수 안에 main() 함수를 호출하는 방식임
    ```c

    _start() {
        // ... something
        main();
        // ... something
        exit(status);
    }

    int main() {

    }
    ```
> main() 함수에서 return 0;은 exit() 호출과 큰 차이가 없음    

---
## exit() 시스템콜
- 부모 프로세스는 status&0377(비트연산) 계산값으로 자식 프로세스 종료 상태 확인 가능 
```c
#include <stdlib.h>
void exit(int status);
```
- 기본 사용 예 
```c
exit(EXIT_SUCCESS); // EXIT_SUCCESS는 0 
exit(EXIT_FAILURE); // EXIT_FAILURE는 1
```

- exit() 시스템콜 주요동작
  - atexit()에 등록된 함수 실행 
  - 열려있는 모든 입출력 스트림 버퍼 삭제(stdin, stdout, stderr)
  - 프로세스가 오픈한 파일을 모두 닫음
  - tmpfile() 함수를 통해 생성한 임시파일 삭제
    - 참고: tmpfile() - 임시파일을 wb+(쓸 수 있는 이진파일 형태) 모드로 오픈 가능 
  ```c
  #include <stdio.h>
  FILE *tmpfile(void);
  ```    

---
## atexit() 함수
- 프로세스 종료시 실행될 함수를 등록하기 위해 사용
- 등록된 함수를 등록된 역순서대로 실행

```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
    void exithandling(void);
    void goodbyemessage(void);
    int ret;

    ret = atexit(exithandling);
    if(ret != 0) perror("Error in atexit\n");
    ret = atexit(goodbyemessage);
    if(ret != 0) perror("Error in atexit\n");
    exit(EXIT_SUCCESS);
}

void exithandling(void) {
    prinf("exit handling\n");
}

void goodbyemessage(void) {
    prinf("see you again!\n");
}
```  