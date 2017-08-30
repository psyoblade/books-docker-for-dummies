# [도커 무작정 따라하기](https://www.slideshare.net/pyrasis/docker-fordummies-44424016)
> 도커에 대한 이해를 돕기 위해 만든 문서입니다.

## 컨테이너 기술?
> 가상화 기술의 장점에도 불구하고, Hypervisor 의 병목에 따른 성능 저하문제가 발생했고, 항상 운영체제를 포함해야 하므로 배포 시의 용량이 커질 수 밖에 없는 문제점들이 있으며, 이러한 배포 및 관리 운영에 대한 기능이 부족한 점 등이 문제점으로 대두되었다. 그래서 이를 해소하기 위해 반 가상화 컨테이너 기술이 등장했고, 컨테이너 내부에 가상 공간을 만들지만, 실제 실행 파일은 호스트 장비에서 직접 실행되어 host 와 kernel 등을 공유하여 성능을 보장받을 수 있었다. 초기에는 리눅스 컨테이너(LXC)를 사용했으나 0.9 버전부터는 libcontainer를 개발하여 사용하고 있다.
* 가상화 기술과 다르게 직접 kernel을 통해 실행되기 때문에 리소스(메모리, 파일, 네트워크 등)에 대한 성능저하가 거의(95~99%) 없다.
* 배포 및 운영에서도 이미지 생성과 배포 시에도 Docker Hub을 통해 손쉽게 배포가 가능하다.


## 왜 가상화도 있는데 컨테이너 기술이 등장 하였나?
> 가상화 기술은 하나의 물리 장비 안에 여러대의 컴퓨터를 구성하는 모델 방식이라 Hypervisor라고 불리우는 레이어가 있어 성능에 병목이 발생한다
* natvie 방식은 비용이, hosted 방식은 virtual os 영역으로 인해 실제 프로그램은 3번째 수준으로 수행되므로 성능에 문제가 발생한다
* [What Container](https://www.docker.com/what-container)


## [하이퍼바이저](http://naleejang.tistory.com/91)란?
> [하이퍼바이저(Hypervisor)](https://ko.wikipedia.org/wiki/하이퍼바이저)는 호스트 컴퓨터(Host Computers) 1대에서 다수의 운영체제(Operating System)를 동시에 실행하기 위한 논리적 플랫폼(Platform)을 말합니다.
* 하드웨어에 직접 설치되는 native (bare metal) 방식
	* Xen 이나 KVM 등을 Hypervisor
* 일반 어플리케이션과 같이 프로그램으로 설치되는 hosted 방식 두 가지가 존재합니다.
	* VirtualBox, VMWare

## 리눅스 커널의 [cgroups, namespaces](https://tech.ssut.me/2017/08/15/what-even-is-a-container)란?
> 리눅스에서 제공하는 컨테이너 기술인 namespaces를 통해 프로그램 간의 격리된 실행 및 관리가 가능하고 cgroups를 통해 자원에 대한 제어를 가능하게 합니다.


## Docker 이미지와 컨테이너는 무엇을 말하나?
> Docker 이미지란? 서버 프로그램, 실행 파일 등을 모두 포함한 패키지. 즉, 저장소에 올리고 받는 물리적인 파일을 자체를 말하며, 컨테이너는 이 이미지를 실행한 상태를 말한다. 
* 이는 곳 하나의 이미지로 여러개의 컨테이너를 실행할 수 있다는 의미이다.


## Docker는 어떻게 이미지를 처리하는가?
> 베이스 이미지가 존재하고, 변경된 부분을 별도의 이미지로 생성 관리하고, 실행 시에 베이스 이미지와 변경된 추가 이미지를 합쳐서 실행하게 된다.
* Docker Hub과 주고 받을 때에도 변경된 이미지만 주고 받으면 된다.


## Immutable Infra Structure 패러다임?
> 호스트 OS와 서비스 운영환경을 (서버 프로그램, 소스 코드, 컴파일 된 바이너리)을 분리하고 한 번 설정한 운영환경은 변경하지 않는다(Immutable)는 개념
* 서비스가 업데이트 되면 운영환경 자체를 변경(업데이트 배포)하지 않고, 이미지를 새로 생성하여 배포한다. 즉 한 번 쓰고 버리는 이미지로 생각할 수 있다.
* Docker는 Immutable Infrastructure를 구현한 프로젝트
	* 여러개의 컨테이너(이미지)를 실행, 저장 및 배포(운반)하는 고래를 형상화 한 로고. Docker는 '부두 노동자'를 말하는데 컨테이너를 다루는 Docker의 기능과 유사하여 붙인 이름.

## 맥에 Docker 설치 및 실행
> [Docker for mac](https://docs.docker.com/docker-for-mac)을 통해 설치하고 실행하면 native 스러워 보이지만 Docker 자체는 리눅스 컨테이너이므로 가상머신에 설치가 되고 해당 CPU, Memory를 Dedicated 하게 잡아주게 된다.
* 버전 및 설치정보 확인
```bash
$> docker version
Client:
    Version:      17.06.0-ce
    API version:  1.30
    Go version:   go1.8.3
    Git commit:   02c1d87
    Built:        Fri Jun 23 21:31:53 2017
    OS/Arch:      darwin/amd64

Server:
    Version:      17.06.0-ce
    API version:  1.30 (minimum version 1.12)
    Go version:   go1.8.3
    Git commit:   02c1d87
    Built:        Fri Jun 23 21:51:55 2017
    OS/Arch:      linux/amd64
    Experimental: true

```
* 도커는 하나의 실행 파일이지만, 서버로도 클라이언트로도 동작합니다. 즉, 클라이언트 명령을 통해 Docker 데몬 서버와 통신하여 결과를 내어 줍니다.


## 주요 명령어
| 옵션 | 설명 | 기타 |
| ------------- |:-------------|:----- |
| run | 새로이 컨테이너를 만들어 실행합니다 | - |
| images | 실행 가능한 이미지 목록을 확인합니다 | - |
| pull | 이미지를 다운로드 합니다 | - |
| rmi | 이미지를 삭제 합니다 | rmi ${CONTAINER\_ID} |
| ps | 현재 실행 중인 컨테이너 확인 | -a 옵션으로 종료된 컨테이너도 확인이 가능합니다 |
| stop | 실행 중인 컨테이너 중지하기 | stop ${CONTAINER\_ID} |
| start | 중지 중인 컨테이너 시작하기 | start ${CONTAINER\_ID} |
| rm | 컨테이너 완전히 제거하기 | 제거되지 않은 컨테이너는 언제든지 재시작이 가능합니다 |
| logs | 컨테이너 로그를 확인 합니다 | -f --tail ${CONTAINER\_ID} 옵션으로 모니터링이 가능합니다. |
| exec | 실행 중인 컨테이너에 명령어를 실행 합니다 | docker exec -it mysql /bin/bash |
* 
```bash
$> docker rm -v $(docker ps -a -q -f status=exited) # 중지된 컨테이너를 일괄 삭제하는 명령은 아래와 같이 실행하면 됩니다.
$> docker exec -it mysql /bin/bash
mysql -uroot
$> docker exec -it mysql mysql -uroot	# 바로 접속하여 명령어를 수행할 수도 있습니다.
```


## 실행 (run) 명령어
| 옵션 | 설명 | 기타 |
| ------------- |:-------------|:----- |
| -d | detached mode 흔히 말하는 백그라운드 모드 | - |
| -p | 호스트와 컨테이너의 포트를 연결 (포워딩) | - |
| -v | 호스트와 컨테이너의 디렉토리를 연결 (마운트) | - |
| -e | 컨테이너 내에서 사용할 환경변수 설정 | - |
| -name | 컨테이너 이름 설정 | - |
| -rm | 프로세스 종료시 컨테이너 자동 제거 | - |
| -it | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 | - |
| -link | 컨테이너 연결 [컨테이너명:별칭] | deprecated |


## 우분투 14.04 실행
```bash
$> docker images
$> docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
$> docker run ubuntu:14.04
Unable to find image 'ubuntu:14.04' locally
14.04: Pulling from library/ubuntu
48f0413f904d: Pull complete 
2bd2b2e92c5f: Pull complete 
06ed1e3efabb: Pull complete 
a220dbf88993: Pull complete 
57c164185602: Pull complete 
Digest: sha256:6a3e01207b899a347115f3859cf8a6031fdbebb6ffedea6c2097be40a298c85d
Status: Downloaded newer image for ubuntu:14.04
```
* 이미지가 없다면 다운로드 받습니다. 그리고 컨테이너는 정상적으로 실행됐지만 뭘 하라고 명령어를 전달하지 않았기 때문에 컨테이너는 생성되자마자 종료됩니다. 컨테이너는 프로세스이기 때문에 실행중인 프로세스가 없으면 컨테이너는 종료됩니다.
```bash
$> docker run --rm -it ubuntu:14.04 /bin/bash
root@7ee200222011:/# cat /etc/issue
Ubuntu 14.04.5 LTS \n \l
root@7ee200222011:/# exit
exit
```
* **유의할 점은 -d 옵션으로 실행하지 않으면 foreground로 실행되어 아무 키도 입력할 수 없게 되므로, Ctrl+C 통해 종료할 수 있습니다.**  


## MySQL 실행
```bash
docker run -d -p 3306:3306 \
> -e MYSQL\_ALLOW\_EMPTY\_PASSWORD=true \
> --name mysql \
> mysql:5.7
Unable to find image 'mysql:5.7' locally
5.7: Pulling from library/mysql
ad74af05f5a2: Pull complete 
0639788facc8: Pull complete 
de70fa77eb2b: Pull complete 
724179e94999: Pull complete 
50c77fb16ba6: Pull complete 
d51f459239fb: Pull complete 
937bbdd4305a: Pull complete 
35369f9634e1: Pull complete 
f6016aab25f1: Pull complete 
5f1901e920da: Pull complete 
fdf808213c5b: Pull complete 
Digest: sha256:96edf37370df96d2a4ee1715cc5c7820a0ec6286551a927981ed50f0273d9b43
Status: Downloaded newer image for mysql:5.7
9cbc6e9f735c7624d85ea01bd404233ca933465ec41a88665f800d61c241eb45n

$> mysql -h127.0.0.1 -uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.19 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> quit
Bye

```
* 위와 같이 백그라운드로 mysql을 설치 및 기동하고, 호스트의 mysql 클라이언트로 접속이 가능합니다.
* [MySQL Hub](https://hub.docker.com/_/mysql/) 사이트에 간단한 사용법과 환경변수에 대한 설명이 존재합니다.


## 도커 준비
* sudo 입력하지 않기
```bash
	sudo usermod -aG docker ${USER}
	sudo service docker restart
```

=======

## TODO
### [슬라이드쉐어](https://www.slideshare.net/pyrasis/docker-fordummies-44424016) 92 페이지 정리 중
### [블로그](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html) 컨테이너 업데이트 정리 중



## 여러가지 단상들...
> 빌드 도구의 fat jar 생성과 github 코드관리가 docker 이미지와 docker hub 구조와 닮아 있으며, 다양한 면에서 아래와 같은 매트릭스를 만들어 보았습니다.

## 생각정리
> 빌드 도구의 fat jar 생성과 github 코드관리가 docker 이미지와 docker hub 구조와 닮아 있으며, 다양한 면에서 아래와 같은 매트릭스를 만들어 보았습니다.
| 비고 | 소프트웨어 | 하드웨어 |
| ---- |:-------------|:----- |
| build | fat jar | docker image |
| repository | github | docker hub |
| concept | class-instance, exec-process | images/container |
* 결국 Mac, Windows 공히 VirtualBox 사용하여 다양한 OS를 올릴 수 있으며, 이를 통해 이미지를 생성 로딩 할 수 있는 것 같습니다.
	* 초기 설치 후에 virutalbox를 통해 boot2docker.ios를 로딩하고, 가상머신에 접속되어 터미널을 만날 수 있습니다.

``` bash
copying initial boot2docker.iso (run "boot2docker.exe download" to update)
initializing...
Generating public/private rsa key pair.
Your identification has been saved in C:\Users\psyoblade.NC-KOREA\.ssh\id_boot2docker.
Your public key has been saved in C:\Users\psyoblade.NC-KOREA\.ssh\id_boot2docker.pub.
The key fingerprint is:
SHA256:
The key's randomart image is:
+---[RSA 2048]----+
|          .      |
|         o o .   |
|         * o B = |
|        . = = =  |
|      . .o *+oo..|
|      . S + + +.+|
|     o +. B o=.o |
|    o E +o +. *  |
|     o * ..o o oo|
+----[SHA256]-----+
Initialization of virtual machine "boot2docker-vm" complete.
Use `boot2docker up` to start it.

starting...
Waiting for VM and Docker daemon to start...
........................oooooo
Started.
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\ca.pem
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\cert.pem
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\key.pem

To connect the Docker client to the Docker daemon, please set:
    export DOCKER_CERT_PATH='C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm'
    export DOCKER_TLS_VERIFY=1
    export DOCKER_HOST=tcp://x.x.x.x:2376

Or run: `eval "$(boot2docker shellinit)"`


IP address of docker VM:
x.x.x.x

setting environment variables ...
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\ca.pem
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\cert.pem
Writing C:\Users\psyoblade.NC-KOREA\.boot2docker\certs\boot2docker-vm\key.pem
    export DOCKER_HOST=tcp://x.x.x.x:2376
    export DOCKER_CERT_PATH='C:\\Users\\psyoblade.NC-KOREA\\.boot2docker\\certs\\boot2docker-vm'
    export DOCKER_TLS_VERIFY=1

You can now use `docker` directly, or `boot2docker ssh` to log into the VM.
```


