# Canvas VS HTML 텍스트 인쇄

웹 리포트 뷰어를 개발할때 여러 후보중 최종적으로 HTML 태그를 선택하게 되었습니다.&#x20;



#### 최우선 과제는 최종 목적지인 인쇄물이 얼마나 잘 나오냐의 문제입니다.&#x20;



브라우저를 100% 비율로 확인시 다음과 같이 폰트사이즈에 상관없이 텍스트 품질에는 별 차이가 없습니다.&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

그러나 브라우저를 200% 비율로 확대시 다음과 같이 캔버스로 표현한 텍스트는 품질 저하가 발생됩니다.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

브라우저 비율이 100%를 사용해야 최고의 품질을 유지할 수 있다. 라고 명시한다면 뷰어기준으로 별 문제없이 사용할 수 있습니다.&#x20;

### 그러나 실제 인쇄물을 인쇄 했을때는 어떨까요?

다음은 윈도우에 내장된 XPS 드라이버를 사용한 예시 입니다.&#x20;

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>위  이미지는  10pt 짜리를 확대한 것 입니다. </p></figcaption></figure>

태그는 깔끔하게 나오며, 역시 캔버스는 이미지로 표현되는지 품질이 저하됩니다.&#x20;

해당 드라이버의 dpi는 600 입니다.  실제로 테스트 해보진 못했지만, 저 dpi가 높아짐에 따라서 이미지 품질도 높아질꺼라 생각됩니다.&#x20;



그러나 일반적으로 600dpi 를 사용하는곳이 99%일테니 캔버스에 렌더링한 텍스트를 출력하는건 무리라고 생각됩니다.&#x20;
