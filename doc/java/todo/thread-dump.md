# 스레드 덤프 분석

아래 사이트가 정리가 잘 되어잇음.

1. https://d2.naver.com/helloworld/10963
2. https://brunch.co.kr/@springboot/126




## Java 스레드 배경 지식

네이버 블로그의 내용을 요약.
https://d2.naver.com/helloworld/10963


### 키워드 정리
- 스레드 동기화



- 스레드 상태

  ![Thread State](https://d2.naver.com/content/images/2015/06/helloworld-10963-1.png)  
  - NEW
  - RUNNABLE
  - BLOCKED
  - WAITING
  - TIMED_WAITING

- 스레드의 종류
  - 데몬 스레드(Daemon Thread)
  - 비데몬 스레드(Non-daemon Thread)
  
- 스레드 덤프 획득 관련
  - jps -v
  - jstack PID
  - Java VisualVM
  - kill -3 PID

- 스레드 덤프의 정보
  ```txt
  "pool-1-thread-13"{1} prio{2}=6 tid{3}=0x000000000729a000 nid=0x2fb4 {4}runnable [0x0000000007f0f000]
      java.lang.Thread.State: RUNNABLE
    at java.net.SocketInputStream.socketRead0(Native Method)
    ...
  ```
  - {1} 스레드 이름
  - {2} 우선순위
  - {3} 스레드 ID
  - {4} 스레드 상태
  - 스레드 콜스택
