# Docker

## Docker란?
컨테이너 기반의 가상화 플랫폼.

### Docker 원리
기존의 Linux Container(LXC)의 namespace나 cgroups(control groups)와 같이 프로세스를 격리(isolate)시키는 방식으로 작동합니다.<br/>
Linux Container를 기반으로 개발하였다가 0.9 버전부터는 자체적인 libcontainer 기술을 사용하고 있습니다.

* LXC
```
Container    | [Container1] [Container2] [Container3]

                       Linux Container
                        
Linux Kernel | [namespace] [cgroups]
                        
Hardware     | CPU, Memory, Disk ...
```
<br/>

* Docker
```
             | [Container1] [Container2] [Container3]
             |                                          [libcontainer]
 Docker      |
             | [Linux Container] [libvirt] [systemd-nspawn]

Linux Kernel | [namespace] [cgroups]
                       
Hardware     | CPU, Memory, Disk ...
```
<br/>

### Docker와 Virtual Machine
```
1. 성능적 차이
Docker 컨테이너는 프로세스 단위의 격리된 환경의 가상화 된 공간으로 이루어져 있어 성능적인 손실이 거의 없습니다.
반면 Virtual Machine의 경우 Hypervisor 위에 Guest OS를 올려 기존에 동작하고 있는 OS와 별도의 OS를 구축하기 때문에 
상당한 Overhead가 발생합니다.


2. 이미지 및 배포
 Docker의 경우 구동을 위한 프로그램과 라이브러리만으로 이미지를 만들기 때문에 상대적으로 가볍습니다. 
 그리고 Layer개념을 사용하여 기존에 사용하던 이미지가 A + B (Ubuntu + Java로 가정)로 구성되어 있고
 새로 받는 이미지가 A + C (Ubuntu + MySql)로 구성되어 있다면 C만 받아서 이용이 가능합니다. 
 또한 Docker Hub를 통하여 이미지를 손쉽게 올리거나 내려받을 수 있어 관리에도 용이합니다.
 반면 Virtual Machine의 경우 반드시 OS를 포함하기 때문에 상대적으로 크기가 커질 수 밖에 없습니다.
```
![image](https://user-images.githubusercontent.com/2038633/117537037-82dbcf00-b039-11eb-9ebd-445051fec4f4.png)

*이미지 출저 : https://www.docker.com/resources/what-container


### Docker가 유용한 이유
1. 개발과 유지운영
2. 독립성과 확장성

## Getting Started

### Installation

* 설치에 필요한 패키지를 설치합니다.
```
$ sudo apt-get update && sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
<br/>

* 도커의 공식 GPG키와 패키지 저장소를 추가합니다.
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
<br/>

* 도커를 설치합니다.
```
$ sudo apt-get update && sudo apt-get install docker-ce
```


### 명령어
```
  * search
  * pull
  * images / rmi
  * run / rm
  * ps
  * start / restart / stop
  * exec
```

## Docker File
```
  - FROM
  - RUN
  - CMD
  - ENTRYPOINT
  - EXPOSE
  - ENV
  - ADD / COPY
  - VOLUMN
```

## Docker Compose

### Installation

* 단일 파일로 이루어져 있어 아래 명령어를 통해 직접 내려받습니다. 최신버전은 Github에서 확인할 수 있습니다.
* https://github.com/docker/compose/releases
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### Getting Started
* docker-compose 는 사전에 설정이 정의된 yaml 파일을 읽어 도커 엔진을 통해 컨테이너를 실행합니다.
* 현재 디렉토리에서 docker-compose.yaml 파일을 읽어 해당 파일을 실행시킵니다.<br/> 
  (build 명령어를 통해 DockerFile을 읽어 실행시키며 없어도 실행 가능합니다.)
* 기존에 docker run으로 실행시키던 구문을 다음과 같이 정의하여 실행 시킬 수 있습니다.

#### docker run
```
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 -v /home/ubuntu/jenkins:/var/jenkins_home jenkins/jenkins
```

#### docker-compose.yaml
* docker-compose up -d 로 실행시킵니다.

```
version : '3.8'             # docker-compose version at least 19.03.0
services:
  jenkins:                  # docker service name
    image: jenkins/jenkins  # docker images
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:                # docker volumn
      - /home/ubuntu/jenkins:/var/jenkins_home
```


* 다음과 같이 여러 서비스들을 정의 할 수 있습니다.
```
version : '3.8'             # docker-compose version at least 19.03.0
services:
  jenkins:                  # docker service name
    image: jenkins/jenkins  # docker images
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:                # docker volumn
      - /home/ubuntu/jenkins:/var/jenkins_home
  mysql:
    image: library/mysql
    ports:
      - "3306:3306"
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: test123!

```


### 구성 
```
  - version
  - services
  - images
  - environment
  - command
  - depends_on
  - ports
  - build
  - volumns
  - links
```

### 실행
```
  - docker-compose up -d
  - docker-compose down
  - docker-compose scale mysql=2
```

 
 
