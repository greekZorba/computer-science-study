# 제 20강 시스템 프로그래밍 핵심 기술 - ABI와 표준
## C 컴파일러
- 유닉스 C 컴파일러 - cc
- 리눅스 C 컴파일러 - GNU cc - gcc(지씨씨)
- 우분투 리눅스에 gcc 설치 
```
$ sudo apt-get install gcc
$ gcc --version

# -o는 원하는 실행파일 이름을 정할 수 있게 하는 옵션 
$ gcc -o test.c(소스코드파일 이름) test(실행파일 이름)
```

---
## ABI(Application Binary Interface)
- 응용 프로그램 바이너리 인터페이스
- 함수 실행 방식, 레지스터 활용, 시스템콜 활용, 라이브러리 링크 방식 등 
- ABI가 호환되면 재컴파일 없이 동작 
- 컴파일러, 링커(라이브 링크), 툴체인(컴파일러를 만드는 프로그램)에서 제공

---
## POSIX 
- 유닉스 시스템 프로그래밍 인터페이스 표준
- IEEE(Institute of Electronic and Electronics Engineers)에서 표준화 시도
- 리차드 스톨만(자유 소프트웨어 재단)이 POSIX를 표준안 이름으로 제안

---
## C 언어 표준
- 다양한 C 언어 변종 
- ANSI(American National Standards Institute)에서 ANSI C 표준 정립 
> 리눅스는 POSIX와 ANSI C 지원 

---
## 시스템 프로그래밍과 버전
- 끊임없는 POSIX, C, C++ 표준 업데이트
- 끊임없는 소프트웨어 업데이트
- 프로그래밍 트렌드와 상관없이 시스템 레벨단 기술은 유지되고, 필요함
> 상위 레벨 프로그래밍을 할지라도, 하위 시스템 레벨을 알고 있으면 더 나은 또는 성능이 개선된 소프트웨어 개발 가능 

