# 데이터 가져가는 외부 프로세스 차단 프로그램

4insuLink 라는곳에서  SmartA 의 데이터를 스크랩해가는 프로그램을 막는 작업

지금 생각해보면 굳이 이런걸 해야 하나 싶지만.

다음과 같은 방법으로 설치 및 실행 체크를 해서 해당 프로그램을 차단.

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

창과 방패의 싸움으로 패치하더라도, 해당 업체에서 바로 리플렉터로  소스를  분석하기 때문에

기존 난독화 툴을 제거하고, 사용자의 실수를 유도하도록 난독화  처리

(함수 주석을 반대로 작성, 바코드문자열 사용, 일본어변수 사용, 쓰레기 변수사용 후 아무작업안함 ,  실제 핵심 로직용 exe  추가 후 아무 의미없는 이름사용  등)

