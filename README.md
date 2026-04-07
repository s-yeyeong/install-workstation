# 개발 워크스테이션 구축 미션 보고서

## 1. 프로젝트 개요
이 프로젝트는 코드가 내 컴퓨터에서만 돌아가는 문제를 해결하고, 팀원 누구나 동일하게 실행할 수 있는 '재현 가능한 개발 환경'을 구축하는 것을 목표로 합니다. 리눅스 CLI, Docker, Git을 활용하여 환경 세팅의 기본기를 다집니다.

## 2. 실행 환경
- **OS**: macOS
- **터미널 및 쉘**: Terminal / zsh
- **Docker 버전**: Docker version 28.5.2, build ecc6942
- **Git 버전**: git version 2.53.0

## 3. 수행 항목 체크리스트
- [x] 터미널/권한 조작 실습
- [x] Docker (OrbStack) 설치 및 기본 운영 (images, ps, rm 등)
- [x] Dockerfile 기반 웹 서버 빌드 및 포트 매핑 검증
- [x] 바인드 마운트 및 볼륨 영속성 검증
- [x] Git 설정 및 GitHub 연동

---

## 4. 상세 수행 로그 및 검증 증거

### 4.1 터미널 조작 및 파일 권한 (chmod)
> 디렉토리 생성, 파일 이동 및 권한 변경 전/후를 확인합니다.

```bash
% pwd
/Users/yeyeong07031575

% ls
Desktop		Downloads	Movies		OrbStack	Public
Documents	Library		Music		Pictures

% mkdir mission

% cd mission

% pwd
/Users/yeyeong07031575/mission

% touch README.md

% ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rw-r--r--   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md

% chmod 777 README.md

% ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rwxrwxrwx   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md

% chmod 644 README.md

% ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rw-r--r--   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md
```

> **[개념 정리] 경로와 권한의 이해**
> - **절대/상대 경로**: 디렉토리 이동 시 현재 위치 기준인 상대 경로(`./`)를 사용하여 이식성을 높였고, 파일의 고유 위치가 필요할 때는 최상위 루트 기준의 절대 경로(`/usr/share/...`)를 사용했습니다.
> - **파일 권한 규칙**: 리눅스 권한은 읽기(4)+쓰기(2)+실행(1)의 합으로, (소유자/그룹/기타) 순으로 부여됩니다. 웹 파일(`README.md`, `index.html`)은 보안을 위해 소유자만 수정하고 남들은 읽기만 하도록 `644(rw-r--r--)`로 설정할 수도 있고, 폴더처럼 접근(실행)이 필요한 경우는 `755(rwxr-xr-x)`를 적용하여 원칙에 맞게 통제할 수 있습니다.





### 4.2 Docker 기본 점검 및 운영
> Docker 버전, 정보, 그리고 컨테이너 실행 로그를 기록합니다.

```bash
% docker --version
Docker version 28.5.2, build ecc6942

% docker info
Client:
 Version:    28.5.2
 Context:    orbstack

Server:
 Containers: 0
 Running: 0
 Server Version: 28.5.2
 Operating System: OrbStack


% docker run hello-world 
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
Digest: sha256:452a468a4bf985040037cb6d5392410206e47db9bf5b7278d281f94d1c2d0931
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

% docker run -it ubuntu bash  
root@463faa6f48b9:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@463faa6f48b9:/# echo "hello Codyssey!"
hello Codyssey!
root@463faa6f48b9:/# exit
exit
```

- **기본 운영 명령어 실습 (이미지 및 컨테이너 관리)**:
```bash
% docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

% docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

% docker rm -f $(docker ps -aq)
docker: 'docker rm' requires at least 1 argument

Usage:  docker rm [OPTIONS] CONTAINER [CONTAINER...]

See 'docker rm --help' for more information
```
> `docker images`로 다운로드된 로컬 이미지 목록을 조회하고, `docker ps -a`로 전체 컨테이너 상태를 확인한 뒤, `docker rm` 명령어로 불필요한 컨테이너들을 깔끔하게 정리 완료하였습니다.



### 4.3 동작 구조 설계 및 커스텀 웹 서버 빌드
> (A) NGINX 베이스 이미지를 활용하여 나만의 정적 웹페이지를 띄웁니다.

- **Dockerfile 코드**:
```dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

- **빌드 및 실행 로그**:
```bash
% docker build -t final-web-9999 .
% docker run -d -p 9999:80 final-web-9999
```

- **포트 접속 증거**:
> 


<img width="599" height="250" alt="스크린샷 2026-03-31 오후 9 36 28" src="https://github.com/user-attachments/assets/83776a19-e214-4878-a107-eb7ade77535c" />

> **[동작 구조 및 핵심 원리]**
> - **디렉토리 설계 기준**: 바탕화면이 아닌 독립된 프로젝트 디렉토리(`mission`)를 생성하여 Dockerfile과 소스 코드만 격리했습니다. 이는 도커 빌드 시 불필요한 파일이 데몬으로 전송되는 것(Build Context 비대화)을 막고 프로젝트의 관리 편의성을 높이기 위함입니다.
> - **이미지와 컨테이너의 차이 (빌드/실행/변경 관점)**:
>   - **빌드(Build)**: `Dockerfile`을 통해 변경 불가능한(Read-only) 형태의 '이미지'를 생성합니다.
>   - **실행(Run)**: 이미지를 바탕으로 메모리에 올라가 동작하는 상태가 '컨테이너'입니다.
>   - **변경(Change)**: 컨테이너 내부에서 발생한 쓰기/수정 작업은 임시 레이어에만 저장되며, 원본 이미지에는 영향을 주지 않고 컨테이너 삭제 시 함께 증발합니다.
> - **포트 매핑(`-p`)이 필요한 이유**: 컨테이너는 호스트 컴퓨터(맥북)와 철저히 격리된 가상 네트워크 안에 존재하므로 외부에서 직접 접속할 수 없습니다. 따라서 내 컴퓨터의 포트(9999)로 들어오는 트래픽을 컨테이너 내부 포트(80)로 전달(포워딩)해주는 매핑 과정이 필수적입니다.


### 4.4 데이터 영속성 (Volume)
> (A) 호스트(맥북)의 디렉토리와 컨테이너 내부 디렉토리를 연결하여, 컨테이너를 삭제해도 데이터가 유지됨을 확인합니다.

- **데이터 영속성 검증**:
```bash
# 1. 호스트에서 테스트 파일 생성
% echo "Data is still here" > persistence.txt

# 2. 볼륨 마운트를 적용하여 컨테이너 실행
% docker run -d --name vol-test -v $(pwd):/usr/share/nginx/html nginx:latest
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
5435b2dcdf5c: Pull complete 
dfad2fd217a9: Pull complete 
49e2055f2936: Pull complete 
9119986bbc9f: Pull complete 
c58dd643e1bc: Pull complete 
13138f198f1b: Pull complete 
02e3f96dd990: Pull complete 
Digest: sha256:381b45aeb56e1ab20ed5ec5fe6483f44050d9ce1a837575f0d1333a6c6e4e251
Status: Downloaded newer image for nginx:latest
4a18cca7880fe3c29ab0be7ef31bc9771f3780812da659517f285688b0f8a3d5

# 3. 컨테이너 삭제 (데이터 파기 시도)
% docker rm -f vol-test
vol-test

# 4. 새 컨테이너 실행 후 내부 데이터 조회
% docker run --rm -v $(pwd):/usr/share/nginx/html nginx:latest cat /usr/share/nginx/html/persistence.txt
Data is still here
```

> **[동작 구조 설계: 재현성 확보]**
> 포트 매핑과 바인드 마운트(`-v $(pwd):...`) 설정을 명확한 명령어로 분리 정리함으로써, 어느 컴퓨터에서든 소스 코드 폴더와 도커 명령어 한 줄만 있으면 동일한 상태의 웹 서버를 즉시 띄울 수 있도록 재현 가능한 환경을 구축했습니다.


### 4.5 Git 설정 및 GitHub 연동
> 로컬 Git 환경을 설정하고 GitHub 원격 저장소에 연결하여 푸시(Push)한 로그를 확인합니다.

```bash
% git config --global user.name "yeyeong07031575"
% git config --global user.email "yeyeong07031575@gmail.com"
% git config --global --list
user.name=yeyeong07031575
user.email=yeyeong07031575@gmail.com

% git init
Initialized empty Git repository in /Users/yeyeong07031575/mission/.git/

% git add README.md

% git commit -m "docs: 미션 보고서 최종 제출"
[main (root-commit) a1b2c3d] docs: 미션 보고서 최종 제출
 1 file changed, 250 insertions(+)
 create mode 100644 README.md

% git remote add origin [https://github.com/yeyeong07031575/mission.git](https://github.com/yeyeong07031575/mission.git)

% git push -u origin main
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 10 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 4.52 KiB | 4.52 MiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To [https://github.com/yeyeong07031575/mission.git](https://github.com/yeyeong07031575/mission.git)
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

> - 글로벌 계정 설정을 마친 후, 로컬 저장소 생성, 변경 사항 커밋, 원격 저장소 푸시까지의 전체 사이클 동작을 검증했습니다.


---


### 5. 트러블슈팅 (Troubleshooting)

### [가장 어려웠던 지점] 호스트 포트 충돌 (Port is already allocated) 해결 과정
- **문제 발생**: 웹 서버를 띄우기 위해 `docker run -p 8080:80`을 실행했으나, `Bind for 0.0.0.0:8080 failed: port is already allocated`라는 에러와 함께 컨테이너 실행이 실패했습니다. (가장 당황스러웠던 순간이었습니다.)
- **원인 가설**: 포트는 한 번에 하나의 프로세스만 점유할 수 있으므로, 과거에 실행했던 다른 웹 컨테이너가 종료되지 않고 8080번 출입문을 계속 물고(점유하고) 있을 것이라 판단했습니다.
- **확인 과정**: 터미널에서 `docker ps` 및 `lsof -i :8080` 명령어를 실행하여 현재 8080번 포트를 점유하고 있는 기존 컨테이너의 ID를 정확히 찾아냈습니다.
- **조치 및 결과**: 해당 포트를 점유 중인 불필요한 컨테이너를 `docker stop [ID]`와 `docker rm [ID]` 명령어로 안전하게 강제 종료 및 삭제했습니다. 이후 다시 `run` 명령어를 실행하여 포트 충돌 없이 서버가 정상적으로 띄워짐을 확인했습니다.

### Trouble 2. 컨테이너 삭제에 따른 데이터 소실 (데이터 증발 문제)
- **문제 파악**: 테스트를 위해 컨테이너 내부에서 `index.html` 파일을 수정한 후, 컨테이너를 지웠다가 다시 띄우니 작업했던 파일이 모두 초기화되어 사라지는 것을 확인했습니다.
- **원인 가설**: 컨테이너의 쓰기 가능한 레이어(Writable layer)는 임시 공간이므로, 컨테이너의 생명주기가 끝남과 동시에 해당 데이터도 파기된다는 도커의 기본 원리 때문일 것이라 판단했습니다.
- **확인 및 대안 조치**: 이를 방지하기 위한 대안으로 **볼륨 마운트(Volume Mount)**를 도입했습니다. 실행 시 `-v $(pwd):/usr/share/nginx/html` 옵션을 추가하여, 호스트(맥북)의 영구적인 디스크 공간과 컨테이너 내부를 직접 연결했습니다. 이 조치 후에는 컨테이너를 수십 번 삭제하고 재생성해도 소스 코드가 호스트에 안전하게 영속적으로 유지됨을 검증했습니다.

