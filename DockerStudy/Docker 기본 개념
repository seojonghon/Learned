# 도커

**컨테이너(Container):**
컨테이너는 독립적으로 실행되는 소프트웨어 패키지입니다. 컨테이너는 운영 체제 수준의 가상화 기술을 사용하여 애플리케이션과 해당 종속성을 격리된 환경에서 실행합니다. 이를 통해 애플리케이션은 호스트 시스템과 분리되어 동작하며, 호스트 시스템에 영향을 주지 않고 일관된 환경에서 실행될 수 있습니다. 컨테이너는 Docker 이미지를 기반으로 생성되며, 필요한 파일 시스템, 라이브러리, 실행 가능한 파일 등 모든 것을 포함합니다.

**이미지(Image):**
Docker 이미지는 컨테이너를 생성하는 데 사용되는 템플릿입니다. 이미지는 응용 프로그램과 그 종속성, 설정 및 실행에 필요한 모든 구성 요소를 포함합니다. 즉, 이미지는 애플리케이션의 코드와 런타임 환경을 패키징하여 이식성과 재현성을 제공합니다. Docker 이미지는 여러 개의 계층(layer)으로 구성되며, 각 계층은 변경 가능하고 읽기 전용입니다. 이 계층 구조를 통해 이미지 간에 공유할 수 있는 공동 부분을 최대화하고 디스크 사용량 및 다운로드 속도를 최적화할 수 있습니다.

**볼륨(Volume):**
Docker 볼륨은 컨테이너 내부나 호스트 시스템 외부에서 데이터를 저장하고 관리하는 방법입니다. 볼륨은 데이터의 지속성(persistence)과 공유 목적으로 사용됩니다. 예를 들어, 데이터베이스 파일 또는 로그 파일과 같은 중요한 데이터를 볼륨에 저장하여 컨테이너가 삭제되거나 재시작될 때도 데이터가 유실되지 않도록 할 수 있습니다. 볼륨은 호스트와 분리된 독립적인 공간으로서 여러 컨테이너 간에도 공유할 수 있으므로 데이터의 안정성과 유연성을 제공합니다.

워드프레스(WordPress):

워드프레스는 웹 사이트 및 블로그를 만들고 관리하기 위한 오픈 소스 콘텐츠 관리 시스템(CMS)입니다. 워드프레스를 사용하면 비전문가도 쉽게 웹 사이트를 생성하고 콘텐츠를 게시하고 수정

# **Dockerfile 작성 시 유의사항 2 - 도커 이미지 크기가 커지지 않도록 유의**

- 도커 컨테이너 이미지를 빌드하고 배포하는 시간을 단축하기 위해서는 도커 이미지 크기를 최대한 작게 유지하는 것이 중요
- Dockerfile의 모든 명령어는 레이어를 생성하고, 생성된 레이어는 저장
- 실제 컨테이너에서 사용하지 않는 (못 하는) 레이어도 저장 공간을 차지하게 되므로, 불필요한 레이어가 생성되지 않도록 하는 것이 중요
- **Dockerfile을 작성할 때 명령어를 묶어서 실행하는 것이 유익**
- **빌더 패턴 또는 다단계 도커 빌드 등을 기법을 이용해서 이미지를 작게 만드는 것이 필요**

[도커 실습 환경 세팅](https://www.notion.so/1a6e974f1d454308a5948a709e76c6f3?pvs=21)

[도커 구성요소](https://www.notion.so/76b30e28c11540d5a57b0feb35605b89?pvs=21)

[윈도우 cmd로 도커 컨테이너 생성,삭제](https://www.notion.so/cmd-56f8ac2aa57b4414aaa2d091d9bd78de?pvs=21)

[아파치 서버 실습](https://www.notion.so/f1e7fe75c2ae4369855464ff2687f0f1?pvs=21)

[빌더패턴](https://www.notion.so/56a6cbe909f14cca9f11e8b494ec3b96?pvs=21)

[모범사례](https://www.notion.so/34a6578fa052427b97c007ced26bc999?pvs=21)

[컨테이너 이미지 삭제](https://www.notion.so/1a753553d68f41b2b9180c8868bc9534?pvs=21)

[LAB](https://www.notion.so/LAB-26cc6e33d4914cffbc6147970ced4023?pvs=21)

[Docker ignore, ENV](https://www.notion.so/Docker-ignore-ENV-be9f2ccb975e48a49cad93dda822f1da?pvs=21)

[볼륨과 볼륨 Mapping](https://www.notion.so/Mapping-c711afc464cd44eda17246df6a49e64c?pvs=21)

[Tagging , Dangling](https://www.notion.so/Tagging-Dangling-b3c8cffc75da42d48e29638820feabb8?pvs=21)

[도커허브 연동](https://www.notion.so/f4f491eb9dbb4f17b5960d64cf279701?pvs=21)

[호스트와 컨테이너 사이 파일 복사](https://www.notion.so/a11adba578214af9a34cfcf7adfce17a?pvs=21)

[nginx 이미지 생성후 도커 허브에 배포해보기](https://www.notion.so/nginx-4ff1119801c1492f84652cab2d0abb48?pvs=21)

[컨테이너 리소스 현황](https://www.notion.so/f396093dba5d46769b564e196e1ddd11?pvs=21)

[컨테이너 네트워크](https://www.notion.so/89317b93c11d49739762da50aab33e4d?pvs=21)

[Word Press, Docker Compose](https://www.notion.so/Word-Press-Docker-Compose-35fb811e5b3a4b52af59d979cfd9b8c6?pvs=21)

[Flask 웹 생성후 배포](https://www.notion.so/Flask-a35d91116660493f9e70250716b10be4?pvs=21)