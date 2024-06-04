# Synology + Git Server + Gitea

### 1. 제어판 > 터미널 및 SNMP >   SSH 서비스 활성화

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### 2. 패키지 센터에서 앱 설치

* Git Server 설치
* Docker 설치

### 3. Docker 앱 실행후 설정 변경

* 레지스트리 - gitea 검색 - 다운로드

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

* 이미지 - 설치된 gitea 선택후 실행

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

* 일반설정 -> 고급설정

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

* 볼륨 추가

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

* 포트설정\
  기본값 자동으로 하면 접속불가.
* 22번 포트는 컴퓨터에서 NAS로 연결되기 때문에 3022로 변경
* 3000번 포트는 NAS에서 안쓰기 때문에 그대로 연결

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### 4.SSH로 시놀로지 접속

* sudo -i 로루트 계정으로 변경 후 /volume1/docker/gitea/gitea/conf/app.ini 수정

{% code title="app.ini" %}
```ini
[server]
APP_DATA_PATH    = /data/gitea
DOMAIN           = 아이피
SSH_DOMAIN       = 아이피
HTTP_PORT        = 3000
ROOT_URL         = http://아이피:3000/
DISABLE_SSH      = false
SSH_PORT         = 22
SSH_LISTEN_PORT  = 22
LFS_START_SERVER = true
LFS_CONTENT_PATH = /data/git/lfs
LFS_JWT_SECRET   = 0ECCCP_5wmSwVo3GawbAz-WOl_Ltt5NUNkMRqRX5GSI
OFFLINE_MODE     = false
```
{% endcode %}

### 5.  Docker 앱 -> 컨테이너 다시 실행

* 초기설정 및 가입후 시작

\
