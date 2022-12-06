## 개요

<img src="https://eocis.app/wp-content/uploads/2022/12/1.png" width="1200">

Bare Metal, HyperVisor 그리고 Container까지의 발전 과정은 “비용과 효율”을 중심으로 발전 되어왔습니다.
**Bare Metal**은 단일 테넌트 서버에 여러 서비스를 올리게 된다면 안정성의 문제와 시스템 부하 관리에 어려움이 있어 서비스 별로 서버를 구성하였습니다.

**Hypervisor**가 등장하면서 단일 테넌트 서버에서 다른 서버를 올릴 수 있는 가상화를 하여 Bare Metal의 여러 단점을 보완하였습니다.
여러 장점이 있지만 서버 설치 및 구성의 간소화와 리소스 분할 및 비용 할당에 있어 Bare Metal보다 유리한 부분이 있습니다.

여기에 **Container**가 등장하고 Hypervisor와의 차이점은 크게 성능 향상 및 유연성입니다.
Guest OS를 설치하는 대신 Host OS의 커널(kernel)을 사용하고 Container에서 커널을 사용하여 Linux의 여러 종류의 OS를 사용할 수 있는 장점이 있으며 Guest OS가 필요하지 않아 매우 가볍고 빠른 장점이 있습니다.
또한, 컨테이너는 하드웨어와 관련이 없기에 유연성이 뛰어납니다.
하지만 커널을 공유한다는 것은 Multi OS를 Native로 실행할 수 없어 리소스 손실이 발생한다는 점입니다.

만약, Host OS를 Windows로 올리게 된다면 기본적으로 Windows의 커널을 공유하기 때문에 Linux Container를 띄우기 위해서는 Windows 내에서 Hypervisor를 사용하여 Linux Container를 실행하게 해야 하는 단점이 있습니다.
하지만 이 경우는 아래 그림과 같이 Hypervisor과 Container를 함께 사용함으로써 해결이 가능합니다.

<img src="https://eocis.app/wp-content/uploads/2022/12/2.png" width="600">

### 컨테이너란?

간단히 말해 컨테이너는 호스트 시스템의 다른 모든 프로세스와 격리된 시스템의 샌드박스 프로세스입니다.이러한 격리 는 오랫동안 Linux에 있었던 기능인 [커널 네임스페이스 및 cgroup 을 활용합니다.](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504)Docker는 이러한 기능을 접근하기 쉽고 사용하기 쉽게 만들기 위해 노력했습니다.

- 이미지의 실행 가능한 인스턴스입니다. DockerAPI 또는 CLI를 사용하여 컨테이너를 생성, 시작, 중지, 이동 또는 삭제할 수 있습니다.
- 로컬 머신, 가상 머신에서 실행하거나 클라우드에 배포할 수 있습니다.
- 이식 가능합니다(모든 OS에서 실행 가능).
- 다른 컨테이너와 격리되며 자체 소프트웨어, 바이너리 및 구성을 실행합니다.

## 1. Docker 작업 수행 명령어

Docker를 사용하여 컨테이너를 컨트롤하는 방법과 Container Image를 생성, 배포 및 관리하는 방법을 설명합니다.

### Container Image란?

컨테이너 이미지는 컨테이너를 생성할 수 있는 실행 코드가 포함된 정적 파일입니다.
컨테이너 이미지는 수정할 수 없으며 모든 환경에서 일관되게 배포할 수 있습니다.

#### 1. 컨테이너 실행

```
docker run nginx
```

> 컨테이너 실행 명령어이며 docker create && docker start 역할을 합니다.
docker create 명령어에 대한 설명은 
포트포워딩을 하지 않아 외부에서 접속은 안되지만 내부 접속은 가능
> 

```
docker run -it nginx:latest /bin/bash
```

> -i 옵션은 STDIN(표준입력) 상태를 열고, -t 옵션은 tty(터미널)를 확보한다.
컨테이너를 종료하지 않은채로, 터미널의 입력을 계속해서 컨테이너로 전달하기 위해서 사용한다.
따라서, -it  옵션은 특히 컨테이너의 쉘(shell)이나 CLI 도구를 사용할 때 매우 유용하게 사용된다.
> 

```
docker run -d --name nginx-test -p 80:80 nginx
```

> -d는 detached 모드입니다.
보통 데몬 모드라고 부르며 컨테이너가 백그라운드로 실행된다.
> 

#### 2. 컨테이너 종료(중지)

```
docker stop <Container ID>
```

```
docker start <Container ID>
```

> 컨테이너에서 실행되는 모든 프로세서를 종료 및 시작합니다.
> 

```
docker pause <Container ID>
```

```
docker unpause <Container ID>
```

> 컨테이너에서 실행되는 모든 프로세서를 일시중지(pause, SIGSTOP)하고 재개(unpause, SIGCONT)합니다.
SIGSTOP, SIGCONT는 리눅스의 kill 명령과 함께 사용됩니다.
> 
    
#### 3. 컨테이너 상태확인

```
docker ps
```

> 현재 실행 및 중지(pause) 컨테이너 확인
> 

```
docker ps -a
```

> 실행중, 중지(pause), 종료된(stop) 컨테이너 모두 확인
> 
    
#### 4. 컨테이너 삭제

```
docker rm <Container ID>
```

> 컨테이너 삭제(종료(stop) 되지않은 상태의 컨테이너는 삭제불가)
> 

```
docker rm -f <Container ID>
```

> 컨테이너 삭제(실행중, 중지(pause), 종료(stop) 컨테이너 모두 삭제)
> 

#### 5. 컨테이너 이미지 관리

```
docker images
```

> 컨테이너 이미지 조회(로컬에 저장된 이미지)
> 

```
docker rmi <Image ID>
```

> 컨테이너 이미지 삭제
> 

#### 6. 컨테이너 이미지 가져오기

[도커 허브](https://hub.docker.com/)에 가면 여러가지 이미지가 있으며 언제든지 사용할 수 있습니다.
사람들이 만들어놓은 이미지를 가져와 사용할 수 있으며 반대로 내가 만든 이미지를 업로드 할 수 있습니다.

```
docker pull <Image>:<Version>
```

> docker hub 또는 퍼블릭 레지스트리에서 이미지를 로컬로 다운 받습니다.
> 

```
docker pull nginx
```

> nginx의 최신 이미지를 로컬저장소로 가져옵니다.
> 

```
docker pull nginx:1.22.1-alpine
```

> alpine을 base image로 사용한 nginx 1.22.1버전의 이미지를 로컬저장소로 가져옵니다.
> 

#### 7. 컨테이너 이미지 생성 및 태깅
! 컨테이너 빌드에 대한 내용만 다룰뿐 실제 빌드과정은 아래에 있습니다.

Dockerfile 작성

```
# Step 1
FROM ubuntu:22.04

# Step 2
RUN apt-get update && apt-get install -y nginx

# Step 3
COPY index.html /usr/share/nginx/html/
```

> Step 1. ubuntu 22.04 버전을 Base Image로 사용합니다.
Step 2. 패키지 툴을 업데이트하고 nginx를 설치합니다.
Step 3. index.html을 Container의 /usr/share/nginx/html로 복사합니다. (index.html 처럼 상대경로도 사용이 가능합니다.)

다음은 Dockerfile에서 자주 사용하는 명령문입니다.
(전체 명령문은 [Docker docs](https://docs.docker.com/engine/reference/builder/)에서 확인 가능합니다.

| FROM | 베이스 이미지 지정 |
| --- | --- |
| RUN | 명령 실행 |
| CMD | 컨테이너 실행 명령 |
| LABEL | 라벨 설정 |
| EXPOSE | 포트 익스포트 |
| ENV | 환경변수 |
| ADD | 파일/디렉토리 추가 |
| COPY | 파일 복사 |
| ENTRYPOINT | 컨테이너 실행 명령 |
| VOLUME | 볼륨 마운트 |
| USER | 사용자 지정 |
| WORKDIR | 작업 디렉토리 |
| ARG | Dockerfile 에서의 변수 |
| ONBUILD | 빌드 완료 후 생성된 이미지가 다른 Dockerfile에서 FROM으로 불러질 때실행되는 명령 |
| HEALTHCHECK | 컨테이너의 헬스 체크 |
| SHELL | 기본 쉘 설정 |

```
docker build <path/to/Dockerfile>
```

> Dockerfile에 작성된 내용을 바탕으로 이미지를 생성합니다.
Dockerfile로 정상적인 빌드가 불가능하면 파일작성에 오류가 있을수도 있습니다.
[Docker docs](https://docs.docker.com/engine/reference/builder/)를 참고합니다.
> 

```
docker build -f <path/to/Dockerfile>
```

> -f 옵션을 사용하여 사용할 Dockerfile을 지정할 수 있습니다.
-f 옵션을 생략하면 상대경로의 Dockerfile을 찾아 build를 수행합니다.
-f 옵션 사용시 Dockerfile 파일 내부에서 선언한 상대경로가 있다면 이역시 고려해야합니다.
> 

```
docker build -t <Image Name>:<Version> <path/to/Dockerfile>
```

> 이미지 빌드시에 이미지 이름과 버전을 정의하는 태그를 넣을 수 있습니다.
> 

#### 8. 컨테이너 빌드 및 테스트

	```
	.
	├── html
	│   └── index.html
	└─── Dockerfile
	```

> 테스트를 위한 index.html을 작성하고 nginx 컨테이너에 배포하는 빌드를 진행합니다.
> 

```
mkdir html
```

> html 디렉터리 생성
> 

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<h1>HI</h1>
<a href="https://eocis.app">This page was customized</a>
</body>
</html>
```

> index.html 파일에 내용을 작성합니다.
> 

```
# nginx 이미지 사용
FROM nginx:latest

# index.html 변경(복사)
COPY index.html /usr/share/nginx/html/
```

> nginx 이미지에 기본 index.html 파일을 변경하는 명령문을 작성합니다.
> 

```
docker build -t nginx-customize:v1.0 -f ./Dockerfile . 
```

```
docker images
```

> 빌드 후 이미지가 정상적으로 생성되었는지 확인합니다.
> 
> 
> ```
> ❯  docker build -t nginx-customize:v1.0 -f ./Dockerfile .                                                                                                                                   16:58:45 [git:main] ✖  
> [+] Building 0.1s (7/7) FINISHED                                                                                                                                                                                    
>  => [internal] load build definition from Dockerfile                                                                                                                                                           0.0s
>  => => transferring dockerfile: 36B                                                                                                                                                                            0.0s
>  => [internal] load .dockerignore                                                                                                                                                                              0.0s
>  => => transferring context: 2B                                                                                                                                                                                0.0s
>  => [internal] load metadata for docker.io/library/nginx:latest                                                                                                                                                0.0s
>  => [internal] load build context                                                                                                                                                                              0.0s
>  => => transferring context: 380B                                                                                                                                                                              0.0s
>  => CACHED [1/2] FROM docker.io/library/nginx:latest                                                                                                                                                           0.0s
>  => [2/2] COPY html/index.html /usr/share/nginx/html/                                                                                                                                                          0.0s
>  => exporting to image                                                                                                                                                                                         0.0s
>  => => exporting layers                                                                                                                                                                                        0.0s
>  => => writing image sha256:2b63bee7521fb3ae542554c6cf4c72c992d0d20603b4a84bada6503b8d44abd5                                                                                                                   0.0s
>  => => naming to docker.io/library/nginx-customize:v1.0                                                                                                                                                        0.0s
> ```
> 
> ```
> ❯  docker images                                                                                                                                                                            16:58:53 [git:main] ✖ 
> REPOSITORY        TAG       IMAGE ID       CREATED         SIZE
> nginx-customize   v1.0      2b63bee7521f   7 minutes ago   135MB
> ```
> 

```
docker run -d -p 80:80 nginx-customized:v1.0
```

> 컨테이너를 실행하고 웹브라우저에 [http://localhost](http://localhost)에 접속하여 확인합니다.
> 

####  9. 컨테이너 접속
docker exec 명령을 통해 실행 중인 컨테이너에서 새 명령을 실행합니다.
해당 명령어를 통해 실행되는 명령어는 컨테이너의 기본 프로세스(PID 1)가 실행되는 동안에만 실행되며 컨테이너를 다시 시작해도 다시 시작되지 않습니다.
    
```
docker exec -it <Container ID> /bin/bash

cd /var/www/html

cat /usr/share/nginx/html/index.html
```

> 이전에 만들고 실행시킨 nginx-customize 컨테이너에 접속하여 작성한 html이 정상적으로 존재하는지 확인합니다.


#### 10. docker commit, push, pull, tag

컨테이너의 파일 변경 사항이나 설정을 새 이미지로 커밋하는 것이 유용할 수 있습니다.
컨테이너 내부에 접속하여 코드나 설정을 변경한 경우 현재 실행중인 컨테이너 자체를 이미지로 만들어, 
이를 통해 대화형 셸을 실행하여 컨테이너를 디버깅하거나 작업 데이터 세트를 다른 서버로 내보낼 수 있습니다.
일반적으로 Dockerfile을 사용하여 문서화되고 유지 관리 가능한 방식으로 이미지를 관리하는 것이 좋습니다.


```
docker exec -it <Container ID> /bin/bash

apt-get update && apt-get install git

exit
```


>위 명령어를 실행할 경우 실행중인 컨테이너에 git 패키지가 설치됩니다.
이는 컨테이너가 삭제되고 다시 컨테이너가 올라올 경우 변경사항은 모두 삭제됩니다.
이때 삭제하기 전 변경사항을 이미지화 하는 명령어는 아래와 같습니다.

```
docker commit <Contaner ID> <Image_name>
-> docker commit 0f8f32bd98f7 nginx-customize-git:v1.0

docker images
```

> 성공적으로 커밋이 되면 아래와 같이 이미지가 추가로 생성됩니다.
> 
> 
> 
```
> docker images                                                                                                                                                                            17:21:48 [git:main] ✖  
> REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
> nginx-customize-git   v1.0      236d40e121fd   4 seconds ago    228MB
> nginx-customize       v1.0      2b63bee7521f   23 minutes ago   135MB
```

#### 11. Docker hub에 이미지 올리기
Docker hub에 이미지를 올리기위해서는 Image의 태그를 변경해주어야 합니다.

```
docker image tag <Image> <Change Image>
```

```
docker image tag nginx-customize:v1.0 docker.io/eocis/nginx-customize:v1.0
```

> 기존 이미지 이름 앞에 “ docker.io/<Docker ID\>/ ” 를 입력합니다.
> 

```
docker push docker.io/<Docker ID>/<이미지:태그>
```

```
docker push docker.io/eocis/nginx-customize:v1.0
```

> push중 denied가 발생한다면 아래 명령어를 입력하여 docker에 로그인을 진행합니다.
(docker 계정이 없다면 생성하시면 됩니다.)
> 
> 
> ```
> docker login
> ```
> 

완료가 된다면 [Docker hub](https://hub.docker.com/)에서 이미지 확인이 가능합니다.

<img src="https://eocis.app/wp-content/uploads/2022/12/3.png">
    

### 2. 작업 조회 명령어

1. docker logs

```
docker logs <Container ID>
```

> 컨테이너의 로그를 확인합니다.
출력되는 값은 stdout, stderr입니다.
> 
2. docker images

```
docker images <Container ID>
```

3. docker ps

```
docker ps
```

4. docker inspect

```
docker inspect <image or container>
```

> docker 세부 정보 출력 명령입니다.
-f 옵션을 사용하여 특정 항목만을 출력할 수 있습니다.
아래 명령어는 컨테이너에 할당된 IP 주소를 확인합니다.
> 
> 
> ```
> docker inspect -f "{{ .NetworkSettings.IPAddress }}" <Container ID>
> ```
> 

### Docker 요약

<img src="https://eocis.app/wp-content/uploads/2022/12/4.png" width="600">
