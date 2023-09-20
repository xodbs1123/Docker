## 참고 자료 ##
- **"이재홍의 언제나 최신 Docker"** => https://pyrasis.com/jHLsAlwaysUpToDateDocker
- **"도커 무작정 따라하기** => https://pyrasis.com/docker/2015/02/09/docker-for-dummies



## Docker Desktop ##
https://docs.docker.com/desktop/
- 원 클릭 설치 애플리케이션
- 간단한 GUI를 제공
- 도커 데스크탑에 포함된 것
  - Docker Engine
  - Docker CLI client
  - Docker Buildx
  - Docker Extensions
  - Docker Compose
  - Docker Content Trust
  - Kubernetesopen_in
  - Credential Helper

- 주요 기능
  - 여러 언어 및 프레임워크로 모든 클라우드 플랫폼에서 모든 **애플리케이션을 컨테이너화하고 공유할 수 있는 기능**
  - 도커 개발 환경을 빠르게 설치하고 설정할 수 있음
  - 최신 버전의 쿠버네티스를 포함
  - 기본적으로 Hyper-V 가상화를 통한 빠르고 안정적인 성능을 제공
  - WSL2를 통해 Linux에서 기본적으로 작업 가능

  ## Docker Desktop for Windows 설치 ##
  - 설치 페이지
  https://docs.docker.com/desktop/install/windows-install/
  <br><br>
  - 설치 방법
  https://myanjini.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B0%EC%97%90-%EB%8F%84%EC%BB%A4-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%83%91-%EC%84%A4%EC%B9%98


## 도커(Docker) 개요 ##
- 오픈소스 컨테이너 프로젝트
- AWS, GCP, Azure 등의 **클라우드 서비스에서 공식 지원**
- 복잡한 리눅스 애플리케이션을 **컨테이너로 묶어서 실행**할 수 있음
- 개발, 테스트, 서비스 환경을 **하나로 통일하여 효율적으로 관리**할 수 있음
- 리눅스 커널에서 제공하는 컨테이너 기술을 이용하여 컨테이너(이미지)를 사람들과 **공유** 가능
- GitHub와 비슷한 방식의 **Docker Hub** 제공
- **컨테이너** => 가상화보다 훨씬 가벼운 기술

### 클라우드 서비스 => 네트워크를 통해 가상화 기술을 이용하여 서버를 임대해주는 서비스 ###

- 도커는 게스트 OS를 설치하지 않음
  - 이미지에 서버 운영을 위한 프로그램과 라이브러리만 격리해서 설치
  - 이미지 용량이 크게 줄어듦
  - 호스트와 OS 자원(시스템 콜)을 공유
- 도커는 하드웨어 가상화 계층이 없음
- 이미지 생성과 배포에 특화
- 이미지 버전 관리 제공, 중앙 저장소에 이미지를 올리고 받을 수 있음
- 다양한 API를 제공하여 원하는 만큼 자동화 가능, 개발과 서버 운영에 유용

### 도커 이미지 & 컨테이너 ###
- 이미지는 서비스 운영에 필요한 서버 프로그램, 소스 코드, 컴파일 된 실행 파일을 묶은 형태
- 컨테이너는 이미지를 실행한 상태, 여러 개의 컨테이너 생성 가능
- 이미지 => 실행파일, 컨테이너 => 프로세스2

## 도커 구성 요소 ##
https://docs.docker.com/get-started/overview/#docker-architecture

![image](https://github.com/xodbs1123/Docker/assets/61976898/9baf18f2-a619-4793-8fe2-a3d64bb52104)


## Docker Daemon ##
-  dockerd
-  도커 API 요청을 수신
-  도커 이미지, 컨테이너, 네트워크, 볼륨과 같은 도커 객체를 관리
-  도커 서비스를 관리하기 위해 다른 데몬과 통신 수행

## Client ##
- docker
- 도커 사용자가 도커와 상호 작용하는 기본 방법
- 도커 사용자의 명령을 도커 API를 사용해서 도커 데몬으로 전달
- 하나 이상의 도커 데몬과 통신 가능

## Registry ##
- 도커 이미지 저장소
- 도커 이미지를 저장하고 배포하는 표준 방법
- 퍼블릭 저장소(기본으로 Docker Hub)와 프라이빗 저장소 존재

## 도커 객체 ( Docker Objects) ##
- **이미지**
  - 컨테이너를 생성할 때 필요한 요소 (가상머신을 생성할 때 사용하는 가상머신 이미지 또는 ISO 파일과 비슷한 개념)
  - 여러 개의 계층으로 된 바이너리 파일로 존재
  - 컨테이너를 생성하고 실행할 때 읽기 전용으로 사용됨
  - 저장소(registy)/리포지토리(repository):태그(tag)
- **컨테이너**
  - 도커 이미지로 생성한 해당 이미지의 목적에 맞는 파일이 들어 있는 파일 시스템과 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간
  - 컨테이너는 이미지를 읽기 전용으로 사용하되 이미지에서 변경된 사항만 컨테이너 계층에 저장하므로 컨테이너에서 무엇을 하든지 원래 이미지는 영향을 받지 않음
  - 생성된 컨테이너는 각기 독립된 파일 시스템을 제공받으며 호스트와 분리돼 있으므로 특정 컨테이너에서 어떤 애플리케이션을 설치하거나 삭제해도 다른 컨테이너와 호스트는 변화가 없음

## 도커 컨테이너 라이프사이클 ##
![image](https://github.com/xodbs1123/Docker/assets/61976898/5c70e061-582b-46ea-86ab-fb5ead86c700)

![image](https://github.com/xodbs1123/Docker/assets/61976898/67cec036-74f2-4c05-969c-91bb183e8e97)
