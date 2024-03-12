# Custom Uri Scheme 설치 체크



### **설치유무 체크 방법**

브라우저에서 액션을 취한 후 다른 UI가 포커스를 뺏어가면 설치되었다고 체크함.

&#x20;\
_잘못된 스키마를 만들고 호출하면 설치 체크가 안돼야 정상이지만,  호출 후에_ \
_1. 브라우저의 주소입력창을 선택하거나,_ \
_2. 윈도우 디렉토리 등에 포커스를 주면 설치되었다고 판단함._&#x20;

\
모든 브라우저에서 발생하는 현상.

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

팝업 메시지의 내용은&#x20;

응용프로그램 명과 웹사이트 도메인이 출력됨

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

일반적인 응용프로그램의 설치를 체크하려면 SetTimeout 의 시간을 주고 UI가 표시되기를 기다려야 함.&#x20;

{% hint style="info" %}
i7 8700 상시15% 점유

Winform .Net framework 4.8 기본 프로젝트 -> 300ms 에서 설치됨 표시

Console .Net framework 4.8 기본 프로젝트 -> 1500ms 에서 설치됨 확인

사용자 프로그램의 UI 표시 시간에 따른 timeout을 설정해서 체크를 해야 함.
{% endhint %}

2024.01.23 파이어폭스의 경우 브라우저 보안 경고가 활성화 되면서, 체크가 안됨(실행은 상관없음)

