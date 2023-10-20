# 테두리

### 결론 부터 말씀드리면 테두리가 있는 컨트롤을 만든다면 HTML대신 Canvas로 사용하는 것이 더 좋을 듯 합니다.&#x20;

제가 기존에 알고 있던 테두리는, 모서리를 기준으로 생성되는 것 입니다.&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

그러나 HTML에서는 위와 같은 기능의 테두리가 없습니다.&#x20;

없어도 괜찮지만 언제 문제가 되냐면

사각형 2개를 붙여서 표처럼 만들때 위와 같은 기능이 없으면 맞닿은 면이 두껍게 보입니다.&#x20;

물론 표를 사용해도 됩니다만, 표를 사용함으로 인하여 다른문제가 발생할 가능성이 있습니다.&#x20;

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>외각선은 1px 로 정확히 처리 되었지만, 내부는 2픽셀로 보이는 상태</p></figcaption></figure>



## 구현가능한 테두리 종류

<figure><img src="../../.gitbook/assets/html border.png" alt=""><figcaption></figcaption></figure>

### Border

* 테두리가 추가되고, 그 만큼 태그 사이즈가 증가해서 다른 UI요소에 영향미침
* 두개의 태그를 연속으로 배치시 겹침불가

### Outline

* 외각으로 테두리가 1px 씩 추가되나, 다른 UI에 영향없음.
* 두개의 태그를 연속으로 배치시 외각으로 테두리가 생성되서 1px로 보일줄 알았으나, 다른 컨트롤위로 테두리가 그려지면서 결국 2px 처럼 보임.  그러나 태그의 배경을 설정하고, 컨트롤 순서 정렬을 통해서 1px 처럼 보이게 가능
* 치명적인 단점은 4방향 따로따로 적용 불가능

### box-shadow

* adadsfadskjfox-shadow 사용하고

내부 외부 반반씩 테두리를 적용해야??
