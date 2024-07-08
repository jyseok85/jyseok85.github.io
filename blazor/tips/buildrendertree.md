# BuildRenderTree 팁

Html Tag 대신에 BuildRenderTree를 이용해서 구성할 수 있다는 것이 Blazor의 특징 중 하나 입니다.&#x20;

그러나 어떻게 구성해야 하는지, 제대로된 가이드가 없어서 사용하지 못하는 사람이 대부분 입니다.&#x20;



그러나  VS 의 디버깅시의 디컴파일 기능을 이용하면&#x20;

사용자가 작성한 Html 태그가 어떻게 BuildRenderTree로 변환 되는지 알 수 있습니다.&#x20;



사용방법은 간단합니다.&#x20;

적당히 태그를 만든 후에 태그에 아무 이벤트나, 속성이나 추가합니다.

그리고 그 이벤트나, 속성을 불러오는 부분에 중단점을 적용하고, 디버깅을 합니다.&#x20;



그리고 호출 스택을 확인해보면 바로 상위에 BuildRenderTree에서 호출된것을 알 수 있습니다.

그 라인을 더블 클릭하면 디컴파일이 되면서 Razor 파일이 실질적인 CS코드가 반환됩니다.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

그 CS코드에는 HTML Tag 대신에 BuildRenderTree가 있구요.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

그러나, 굳이 사용해야 하는지 의문이긴 합니다.&#x20;

태그내에서 처리하는게 가시성 및 가독성이 더좋으니까요.&#x20;
