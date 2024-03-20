# Prometheus & Grafana 로 모니터링

<figure><img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgS-LvAZG5tdeb38Yc48e48uXQSjuI6ErYYXV2c7auWSZ-9AkEEQMHU7tw_vULpoUIpUminfyhiAg6L5Rwn80pAVkG9C75t9JCjnLes19EH5pBnGL_rKT__DOXodZEc6JCJoaeXBRIqwQ/s16000/Grafana.jpg" alt=""><figcaption></figcaption></figure>

개발배경 회사에서 운영중인 원격프로그램의 서버가 증성되면서 관리가 힘들어졌기 때문에 , 실시간으로 서버의 상태를 모니터링 해서 장애대응할 필요가 생겼습니다. 원래는 익숙한 Elastic + kibana를 이용할 생각이여있지만 이사님께서 지나가는 말로 프로메테우스를 언급하셨기에, 나중에 비교해서 말씀드리기 위해 프로메테우스로 진행했습니다.

진행해보니 둘다 장단점이 있었는데 확실히 Elastic보다는 학습난이도도 낮고, 사용하기도 더 편리해서 생각보다 빨리 구현할 수 있었습니다.

프로젝트설명

* DB 트랜젝션 모니터링
* WebService 모니터링
* 시스템 상태, 사용량 모니터링
* 장애발생시 회사 메신져로 알리기 위한 웹서비스 개발

담당업무

* 단독개발

사용기술 Prometheus, Grafana, Blazor, Docker

운영하면서.. 모니터링 대상서버에서 수집해오는 형태이기 때문에, 서버를 여러대 늘리거나, 메인이되는 Prometeheus 서버를 변경하는데 어려움이 없었고, 장애 알림을 설정하는것도 간단해서 관리하는데 힘이들지 않았습니다. 단순 시스템 Metric을 모니터링하는 것 뿐이라면 확실히 Elastic보다는 좋은 듯 합니다. 그러나 애플레케이션에서 발생하는 정보들을 수집하는 방법이 번거롭기 때문에 개발&업데이트가 진행되는 구조의 서버를 관리한다면 다른 솔루션을 사용하는것이 더 좋을듯합니다. Prometheus는 안정적으로 변화가 없는 시스템을 모니터링하는쪽이 더 적합한듯한 느낌이 들었습니다.

<figure><img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgcMscLkbm1vC7zdeLcCNNMsMokwFAkirZ6OtEQSNMDKR4oFKbMLqzv61vFI_iZ4NnDNogswoBoYxC_pJzUKuFO_4C2h4Pb8nZE2PTbhPlwbe9j5pTHVCzgvxU8uY9kdBy9Ma-lqlQjqg/s16000/Prom1.jpg" alt=""><figcaption></figcaption></figure>

