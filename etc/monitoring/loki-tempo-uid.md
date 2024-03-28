# Loki 와 Tempo의 UID 연동

로키는 사용자에서 작성된 로그를 프롬테일을 통하여 로키로 데이터를 전송하고,&#x20;

그라파나에서 모니터링하기 위한 도구입니다.&#x20;



이 때 계측하기 위한 TraceID 를 수집도구에서 가져와서 로그 작성시 추가하면,

템포로 전송된 Trace ID와 매칭시킬수 있습니다.&#x20;



즉,  특정 로그가 작성될때 내부적으로 어떤 상황이 진행되는지 로그로부터 추적이 가능 하다는 거죠.



1. 관련모듈을 프로젝트에 참조를 합니다.&#x20;

{% embed url="https://opentelemetry.io/docs/instrumentation/java/manual/" %}

2. Trace ID를 해당 모듈로부터 가져옵니다.&#x20;

{% code title="Trace ID 가져오기" %}
```java
import io.opentelemetry.api.trace.Span;
var traceid = Span.current().getSpanContext().getTraceId();
```
{% endcode %}

3. 평소대로 로그를 작성할때 저 TraceID를 추가해서 작성합니다.&#x20;



4. 로키로 넘어온 데이터를 키:값 형태로 패턴화 합니다.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>패턴화 예제</p></figcaption></figure>

5. 그라파나 로키 데이터 소스에서 해당 패턴화된 데이터를 Derived fields로 설정합니다.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

6. 로키에서 상세로그 확인시 traceid가 템포와 연동된 것을 볼 수 있습니다.&#x20;

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://github.com/open-telemetry/opentelemetry-java-instrumentation#manually-instrumenting" %}



