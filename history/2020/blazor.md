# Blazor 로 구현한 웹 리포트 뷰어



개발배경.Net Framework 버전으로 개발되었던 인쇄모듈은 윈도우에서만 동작되고, 설치를 필요로 했기 대문에 이 단점을 해결한 웹버전으로 개발할 필요성이 생기게 되었습니다. 그래서 후보군으로 생각했던 HTML5 Canvas, Typescript, Unity, Blazor 의 프로토 타입을 개발해보고 최종적으로 가장 합리적인 Blazor를 통하여 개발을 진행하게 되었습니다. \
각 모듈별 채택되지 않은 원인HTML5 Canvas - 렌더링 품질문제(폰트 확대시 계단현상발생).TypeScript - C# 스크립트를 동적으로 실행할 방법이 없음.Unity - 브라우저 인쇄시 현재 렌더된 화면만 인쇄가능.\
\
담당업무- JS , CSS를 제외한 BackEnd 서버 개발, Linux 셋팅, Nginx 웹서버, 로드밸런싱 구성- 현재 유지보수 단계에서는 단독으로 담당중.\


<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

\
사용기술- Net5, Blazor, Nginx, Docker\
개발이슈사항- 막연히 클라이언트로 실행되는 WASM이 성능상 이점이 있을것으로 생각했으나, 현재(2020년) Blazor를 통한 WASM은 싱글스레드로 동작되어 성능이 낮은 문제가 있었습니다. - 더 큰 문제는 WASM에서 브라우저로 데이터를 전달하는 과정에서 Json Serialize 시간이 1MB 당 1초정도로 오래 걸리는 문제가 있었습니다. .Net 버전이 증가함에 따라 속도 향샹이 있었지만 서버처럼 1/100로 속도가 단축되지 않는이상 의미가 없다고 생각되어 Server-Side로 변경을 진행했습니다.    (앞으로 기술 발전에따라 성능이 개선될거라 생각되어 양쪽 버전 전부 동작되게 소스를 관리중입니다.)- Server Side로 작업을 진행하니 다중 사용자에 의한 성능 부하 발생이라는 문제점이 생겨나게 되어 프록시서버를 통한 로드밸런싱 및 다수의 서버에 일관성있는 배포를 위한 도커환경을 구성하게 되었습니다.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
