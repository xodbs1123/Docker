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
