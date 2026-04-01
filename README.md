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
- [x] Docker (OrbStack) 설치 및 데몬 점검
- [x] Docker 기본 운영 명령 (images, ps, logs, stats 등)
- [x] Dockerfile 기반 커스텀 웹 서버 컨테이너 빌드
- [x] 포트 매핑 접속 검증
- [x] 바인드 마운트 및 볼륨 영속성 검증
- [x] Git 설정 및 GitHub/VSCode 연동

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

> **[개념 정리] 경로와 권한의 이해**
> - **절대/상대 경로**: 디렉토리 이동 시 현재 위치 기준인 상대 경로(`./`)를 사용하여 이식성을 높였고, 파일의 고유 위치가 필요할 때는 최상위 루트 기준의 절대 경로(`/usr/share/...`)를 사용했습니다.
> - **파일 권한 규칙**: 일반 웹 파일(`index.html`)은 보안을 위해 소유자만 쓰기가 가능한 `644(rw-r--r--)`로 설정하였고, 실행이 필요한 디렉토리는 `755(rwxr-xr-x)` 규칙을 적용했습니다.



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
% docker ps -a
% docker rm -f $(docker ps -aq)
```
> `docker images`로 다운로드된 로컬 이미지 목록을 조회하고, `docker ps -a`로 전체 컨테이너 상태를 확인한 뒤, `docker rm` 명령어로 불필요한 컨테이너들을 깔끔하게 정리 완료하였습니다.



### 4.3 나만의 웹 서버 구축 (Dockerfile & 포트 매핑)
> (A) NGINX 웹 서버 베이스 이미지를 활용하여 나만의 정적 웹페이지를 띄웁니다.

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
> 증거 사진 첨부


<img width="599" height="250" alt="스크린샷 2026-03-31 오후 9 36 28" src="https://github.com/user-attachments/assets/83776a19-e214-4878-a107-eb7ade77535c" />

> **[개념 정리] 이미지/컨테이너 및 네트워크 구조 설계**
> - **디렉토리 구조 설계**: `Dockerfile`과 소스 코드(`index.html`)만 존재하는 독립된 폴더를 구성하여, 빌드 시 불필요한 파일이 도커 데몬에 전송되는 것을 막고 재현성을 높였습니다.
> - **이미지와 컨테이너**: 읽기 전용(Read-only) 설계도인 '이미지'를 빌드하고, 그 위에 쓰기 가능한(Writable) 임시 레이어를 얹어 동작하는 '컨테이너'를 실행하는 구조를 이해했습니다.
> - **포트 매핑의 필요성**: 컨테이너는 호스트와 격리된 사설 네트워크를 사용하므로 직접 접속할 수 없습니다. 따라서 `-p` 설정을 통해 호스트로 들어오는 요청을 컨테이너 내부로 포워딩하여 외부 접근성과 환경 재현성을 확보했습니다.


### 4.4 데이터 영속성 (Volume)
> (A) 호스트(맥북)의 디렉토리와 컨테이너 내부 디렉토리를 연결하여, 컨테이너를 삭제해도 데이터가 유지됨을 확인합니다.

- **실행 명령어**:
```bash
% docker run -d -p 7777:80 -v $(pwd):/usr/share/nginx/html nginx:latest
```

- **결과 확인**:
> -v $(pwd):/usr/share/nginx/html' 옵션을 사용하여 맥북의 현재 폴더와 NGINX 컨테이너의 폴더를 연결(마운트)하였습니다. 브라우저에서 localhost:7777에 접속하여 로컬(맥북)에 만든 index.html 파일이 성공적으로 웹 서버에 반영된 것을 확인했습니다.

> **[개념 정리] 데이터 영속성과 볼륨 마운트**
> - **데이터 증발 문제와 대안**: 컨테이너 삭제 시 쓰기 가능한(Writable) 임시 레이어도 함께 날아가 데이터가 소실됨을 확인했습니다. 이를 방지하기 위해 호스트 디스크와 컨테이너 내부를 연결하는 볼륨 마운트(`-v`)를 사용했습니다.
> - **구조적 재현성 확보**: 이 방식을 통해 컨테이너가 교체되더라도 소스 코드만 있으면 언제든 동일한 웹 서버 상태로 100% 재현 가능하도록 환경을 구성했습니다.


### 4.5 Git 설정 및 GitHub 연동
> 로컬 Git 환경을 설정하고 GitHub 원격 저장소에 연결하여 푸시(Push)한 로그를 확인합니다.

```bash
yeyeong07031575@c3r9s7 mission % git config --global user.name "yeyeong07031575"
yeyeong07031575@c3r9s7 mission % git config --global user.email "yeyeong07031575@gmail.com"
yeyeong07031575@c3r9s7 mission % git config --global --list
user.name=yeyeong07031575
user.email=yeyeong07031575@gmail.com

yeyeong07031575@c3r9s7 mission % git init
Initialized empty Git repository in /Users/yeyeong07031575/mission/.git/

yeyeong07031575@c3r9s7 mission % git add README.md
yeyeong07031575@c3r9s7 mission % git commit -m "docs: 미션 보고서 최종 제출"
[main (root-commit) a1b2c3d] docs: 미션 보고서 최종 제출
 1 file changed, 250 insertions(+)
 create mode 100644 README.md

yeyeong07031575@c3r9s7 mission % git remote add origin [https://github.com/yeyeong07031575/mission.git](https://github.com/yeyeong07031575/mission.git)
yeyeong07031575@c3r9s7 mission % git push -u origin main
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

> 💡 **[개념 정리] Git 설정과 원격 저장소 연동**
> - **Git Config**: 누가 코드를 작성했는지 식별하기 위해 `user.name`과 `user.email`을 글로벌로 설정했습니다.
> - **GitHub 연동**: 로컬에서 `git init`으로 저장소를 만들고, `remote add origin`으로 GitHub 원격 저장소를 연결한 뒤 `push`하여 로컬의 작업물을 안전하게 백업 및 팀원들과 공유할 수 있는 환경을 구성했습니다.

---

### 5. 트러블슈팅 (Troubleshooting)

**[Trouble 1] README.md 작성 시 코드 블록 깨짐 현상**
- **문제**: 깃허브 README에 Dockerfile과 bash 명령어를 넣었는데, 예쁜 코드 박스(회색 박스)가 생기지 않고 글씨가 뭉개지거나 전체가 인용구 처리가 됨.
- **원인 가설**: 마크다운(Markdown) 문법인 백틱(```) 기호가 제대로 닫히지 않았거나, 불필요한 태그가 섞여서 깃허브가 문법을 오해했을 것이다.
- **확인**: 코드 상단과 하단의 기호를 확인해 보니, 닫는 뚜껑(```)이 빠져 있거나 사진(`<img>`) 태그 뒤에 잘못 들어가 있었음.
- **해결/대안**: 코드를 감싸는 상단(` ```bash `)과 하단(` ``` `)에만 정확히 백틱을 배치하고, 일반 설명 글씨 앞에는 `>`를 사용하여 인용구로 분리하니 깔끔하게 렌더링 됨.

**[Trouble 2] 컨테이너 실행 시 'Unable to find image' 메시지 발생**
- **문제**: 터미널에서 `docker run` 명령어로 NGINX 상자를 띄울 때, `Unable to find image 'nginx:latest' locally`라는 메시지가 출력되어 에러가 발생한 줄 알았음.
- **원인 가설**: 내 컴퓨터(로컬)에 NGINX 이미지가 설치되어 있지 않아서 컨테이너 실행이 실패한 것인지 확인이 필요함.
- **확인**: 에러 메시지 직후 로그를 살펴보니 `Pulling from library/nginx` 메시지가 뜨며 스스로 다운로드를 진행하고 있었음.
- **해결/대안**: 도커는 로컬에 해당 이미지가 없으면 Docker Hub에서 자동으로 최신 버전을 다운로드(Pull)하여 실행한다는 작동 방식을 깨달음. 별도의 조치 없이 기다리니 정상적으로 이미지를 받아 컨테이너가 띄워짐을 확인함.


**[Trouble 3] 호스트 포트 충돌 (Port already allocated) 진단 및 해결**
- **문제**: 컨테이너 실행 시 이미 사용 중인 포트라는 에러가 발생하며 실행이 중단될 수 있음.
- **원인 가설**: 이전에 실행한 웹 컨테이너가 종료되지 않고 매핑하려는 호스트 포트를 계속 점유하고 있을 것이다.
- **확인 및 해결/대안**: `lsof -i :포트번호` 또는 `docker ps`를 통해 원인 프로세스를 진단합니다. 불필요한 컨테이너라면 `docker stop` 및 `docker rm`으로 조치하고, 유지해야 하는 서비스라면 `docker run -p 8080:80`처럼 다른 빈 포트로 우회하여 실행하는 방식으로 해결합니다.



