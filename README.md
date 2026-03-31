# 🚀 개발 워크스테이션 구축 미션 보고서

## 1. 프로젝트 개요
이 프로젝트는 코드가 내 컴퓨터에서만 돌아가는 문제를 해결하고, 팀원 누구나 동일하게 실행할 수 있는 '재현 가능한 개발 환경'을 구축하는 것을 목표로 합니다. 리눅스 CLI, Docker, Git을 활용하여 환경 세팅의 기본기를 다집니다.

## 2. 실행 환경
- **OS**: (예: macOS Sonoma / Windows 11 / Ubuntu 22.04)
- **터미널 및 쉘**: (예: Terminal / Git Bash / zsh / bash)
- **Docker 버전**: (나중에 확인하고 채워넣을 곳)
- **Git 버전**: (나중에 확인하고 채워넣을 곳)

## 3. 수행 항목 체크리스트
- [ ] 터미널/권한 조작 실습
- [ ] Docker (OrbStack) 설치 및 데몬 점검
- [ ] Docker 기본 운영 명령 (images, ps, logs, stats 등)
- [ ] Dockerfile 기반 커스텀 웹 서버 컨테이너 빌드
- [ ] 포트 매핑 접속 검증
- [ ] 바인드 마운트 및 볼륨 영속성 검증
- [ ] Git 설정 및 GitHub/VSCode 연동

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

yeyeong07031575@c3r9s7 mission % ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rw-r--r--   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md

yeyeong07031575@c3r9s7 mission % chmod 777 README.md

yeyeong07031575@c3r9s7 mission % ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rwxrwxrwx   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md

yeyeong07031575@c3r9s7 mission % chmod 644 README.md

yeyeong07031575@c3r9s7 mission % ls -la
total 0
drwxr-xr-x   3 yeyeong07031575  yeyeong07031575   96  3 31 18:54 .
drwxr-x---+ 19 yeyeong07031575  yeyeong07031575  608  3 31 18:53 ..
-rw-r--r--   1 yeyeong07031575  yeyeong07031575    0  3 31 18:54 README.md
```

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

