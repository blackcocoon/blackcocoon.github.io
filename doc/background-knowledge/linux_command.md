---
sort: 1
---

# Linux Command 
Ref. https://mug896.github.io/bash-shell/index.html


## 1. netstat

```bash
netstat -tonp
```

## 2. watch
명령어 반복 실행

리눅스 명령어 중에 명령어 실행 결과 값을 계속 지켜보고 싶을 때 사용.
shell 스크립트로 작성해도 되지만, 명령어 중에 watch 가 있습니다. 결과 화면은 전체화면으로 나타납니다.
 
```note
 watch -n [시간:초] <명령어>
```

<br/>

예를 들어, 라우팅 갱신 상태를 1초 단위로 확인하고 싶다면
이렇게 하면 main 라우팅 테이블의 상태를 1초마다 확인할 수 있습니다.

```sh
watch -n 1 ip ro
```

<br/>
  
/tmp 디렉토리의 파일 리스트 결과를 1초마다 확인합니다.

```sh
watch -n 1 ls -la /tmp
```

<br/>  
  
Active Internet connections 상태를 1초마다 확인
tcp socket 관련 정보를 주기적 확인 가능

```sh
watch -n 1 netstat -tonp
```


## 3. lsof



## 4. useradd
리눅스 사용자 추가

아래는 서비스 계정 추가방법 예제

```sh
useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```

사용된 옵션 설명

```note
`-s, --shell SHELL`    
The name of the user's login shell.  
The default is to leave this field blank, which causes the system to select the default login shell specified by the SHELL variable in /etc/default/useradd, or an empty string by default.

`-U, --user-group`  
Create a group with the same name as the user, and add the user to this group.   
The default behavior (if the -g, -N, and -U options are not specified) is defined by the USERGROUPS_ENAB variable in /etc/login.defs.

`-m, --create-home`  
Create the user's home directory if it does not exist.   
The files and directories contained in the skeleton directory (which can be defined with the -k option) will be copied to the home directory.  
useradd will create the home directory unless CREATE_HOME in /etc/login.defs is set to no

`-d, --home HOME_DIR`  
The new user will be created using HOME_DIR as the value for the user's login directory.  
The default is to append the LOGIN name to BASE_DIR and use that as the login directory name. The directory HOME_DIR does not have to exist but will not be created if it is missing.
```


## 5. 복수 개의 파일 이름 변경

많은 파일 이름에서 특정 패턴을 찾아서 일괄 변경하기 위한 시도는 오래전부터 있었습니다.  
아마도 가장 흔한 방법이 rename 일텐데, rename의 설치 방법에 따라 동작 여부가 약간 달라 문제가 있죠.

일반 pkg에 built-in되어 있는 rename은 파일명에서 패턴을 찾아 변경하는 옵션이 제공되지 않습니다.  
그래서 shell로 도전하게 되었습니다.

일단 결과부터 놓고 풀어나갑니다.  
아래는 파일 이름에서 A_A를 찾아 B_B로 변경하는 명령어 입니다.

```bash
$ find . -type f  | xargs -I{} sh -c 'mv -v $0 ${0/A_A/B_B}' {} ;
```
하나하나 풀어가며 의미를 알아봅니다.

```bash
$ find . -type f
```
현재 폴더에서 파일 리스트를 가져옵니다.

```bash
$ find . -type f | xargs -I{}
```
가져온 파일 리스트를 xargs에서 {}로 사용하게 될 겁니다.

```bash
$ find . -type f | xargs -I{} sh -c ...
```
새로운 sub-shell로, 앞서 구한 것들을 갖고 무언가(mv)를 할 겁니다.

```bash
$ find . -type f  | xargs -I{} sh -c 'mv -v $0 ${0/A_A/B_B}' {};
```
여기가 핵심인데, 파일 명에서 A_A부분을 찾아 B_B로 변경합니다. (?)  
갑자기 뜬금 없죠?

이걸 이해하기 위해서는 $0의 값을 알아야 할 필요가 있습니다.  
sh의 -c 옵션일때 $0에 대한 도움말을 잠깐 살펴보면 아래와 같습니다.

```note
-c string   

If  the  -c  option is present, then commands are read from string.  
If there are arguments after the string, they  are  assigned  to  the  positional  parameters, starting with $0.
```
string이 끝난 이후에 arguments가 있으면 그것을 $0로 받겠다는 의미 입니다.  
돌아와서 아래를 보죠.

```bash
$ sh -c 'mv -v $0 ${0/A_A/B_B}' {}
```
$0 에는 {}의 값이 들어 있는데 앞서 {}에는 find로 찾은 파일 명이 있을겁니다.

그럼 아래처럼 해석이 되겠네요!   
mv "원본파일이름" "패턴이 적용된 파일 이름"

간단하면서도 shell script의 구성 하나하나를 모르면 해낼 수 없는 코드 입니다.  
누군가 에게 광명이 찾아지기를 바래봅니다.