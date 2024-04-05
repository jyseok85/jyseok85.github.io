# Elastic 및 Kibana로 모니터링

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

개발배경 ReportViewer를 웹용으로 개발을 하게 되면서 다수의 클라이언트나 서버에서 발생하는 정보들을 통합해서 , 오류 확인 및 통계 성능측정을 해야할 필요가 생겼습니다. 기존에 NLog 등을 이용해서 어플리케이션 단위로는 확인했었지만 좀더 체계적이고 관리가능한 프레임워크를 이용하고 싶어서 ElasticSearch 와 Kibana를 선택하게 되었습니다.

프로젝트설명

* 모든 로그를 Elastic에 기록
* 각 스텝별 진행상태 및 수행시간 측정
* 각 서버별 시스템 자원상태 분석
* 유저별 접속 정보 통계 분석

사용기술

* ElasticSearch, Kibana, Metricbeat, Apm

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>
