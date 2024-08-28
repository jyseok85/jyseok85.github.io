# Winform 표시속도개선



<img src="../../.gitbook/assets/file.excalidraw.svg" alt="기존 UI표시까지 7초 소요" class="gitbook-drawing">

기존에는 위와 같은 방법으로 프로그램을 실행했었습니다.&#x20;

비지니스로직(API 호출 및 데이터 다운로드)이후 윈도우 Form dll 이 로드가 되므로 \
Winform기본 로드시간 2\~3초 + 비니지스로직 시간동안 스플래시 창을 보여주면서 대기 하고 있었습니다. \
(빈 프로젝트로 윈폼 하나 만들어 마우스로  클릭해서 눈으로 보이는 시간이 생각보다 오래 걸립니다)



웹에서 버튼을 누르면 보통 7\~8초 정도 이후 실제 프로그램이 보였던 거죠.\
(물론 2\~3초 쯤에 프로그래스 창을 띄워서 로딩중이라고 했습니다)



사용자의 만족도를 향샹시키려면 어떻게 해야할까요?

1. 디자인을 먼저 띄우고 비지니스 로직을 다음에 처리
2. 프로그램을 항상 띄워놓고 안보이게 처리



먼저 1번 방법은 모든 개발에 적용 가능합니다.&#x20;

개발 초기에는 생산성 향상 및 속도 문제가 없으므로 데이터 설정 이후에 페이지 전환이 이뤄지게 되지만, 점점 앞 부분 로직이 커지면서 페이지 전환이 느린 이슈가 생기고 최종적으로는 UI표시 이후 로딩창을 통해서 대기를 하게 됩니다.&#x20;

(이걸 개선하면서 백단 쿼리 최적화나, 로직 개선 등이 이뤄져야 하지만 보통 안되죠..)



<img src="../../.gitbook/assets/file.excalidraw (1).svg" alt="UI를 먼저 띄울경우 4초" class="gitbook-drawing">

2번 방법의 경우 저 Winform 모듈이 로드되는 시간자체를 없애는 방법입니다.&#x20;

대표적으로 게임사 클라이언트가 있습니다. (ex 스팀)



설치 마지막 옵션에 윈도우 시작시 항상시작 옵션을 통해서 메모리에 상주 시키고 다음 실행시 바로 실행되게 하는 옵션입니다.&#x20;



장점은 이미 실행되어 있어서 즉시 실행되는 것처럼 보이는 것이며, 단점은 메모리 점유가 증가한다는 점 입니다.&#x20;



이 경우 마스터 exe 에서 UI가 표시되는 exe로 화면을 표시하라는 명령을 전달해야  합니다.&#x20;

그 방법으로

1. 전통적인 소켓 통신 - 뭔가 통신하면? 어렵죠? 그러므로 전 사용하지 않겠습니다.
2. WCF?? WCF?? - 이건 아닙니다. 구시대적인 기술
3. 파일을쓰고 파일와쳐로 파일을 검색해서 읽기 - 개념자체는 쉬운데 깔끔하지 않죠\
   (가끔씩 보안모듈  때문에 파일 검사되거나, 파일작성 실패가능성도 있고)
4. 메모리 맵 파일(Memory Mapped File) - 처음 봤는데 메모리는 왠지 위험할 것 같은 느낌
5. Win32 API를 통한 방법 - 우리는 2024년을 살고 있습니다. 과거 기술은 놔줍시다.&#x20;
6. Named Pipes - 프로세스 간 통신(IPC: Inter-Process Communication)의 가장 일반적인 방법 중 하나



저는Named Pipes 로 하기로 결정했습니다. 참조할 것도 따로 없고, 기본 기술이고, 코드도 간단하고

{% code title="Server" %}
```csharp
using System.IO.Pipes;
using System.Text;

var server = new NamedPipeServerStream("testpipe");
server.WaitForConnection();

using (var reader = new StreamReader(server))
{
    string message = reader.ReadToEnd();
    Console.WriteLine("Received from client: " + message);
}
```
{% endcode %}

{% code title="Client" %}
```csharp
using System.IO.Pipes;
using System.Text;

var client = new NamedPipeClientStream("testpipe");
client.Connect();

using (var writer = new StreamWriter(client))
{
    writer.Write("Hello from client!");
    writer.Flush();
}
```
{% endcode %}

기존에는 창을 닫기 버튼 누르면 종료시키던 것 대신 안 보이게 처리하고 NamedPipeServerStream 를 통해서 서버를 가동 시킵니다.&#x20;

추후 웹에서 재 호출 시 서버와 커넥션이 맺어진다면 서버에 파라메터를 전송하고, 아니라면 기존 로직대로 프로세스를 실행하면 됩니다.&#x20;



그렇게 된다면 최종적으로 다음과 같은 형태로 최초 7초에서 2초로 단축 가능합니다. \
(실제 제 PC기준 exe 가 실행되는데는 0.2초정도가 소요되어 전체시간이 다르며, 아래 시간은 단순 표시  용  예 입니다)

<img src="../../.gitbook/assets/file.excalidraw (2).svg" alt="최종 결과 2초 만에 UI 표시" class="gitbook-drawing">

