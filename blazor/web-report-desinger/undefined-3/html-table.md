# Html Table 컨트롤 테두리!

기본적으로 테이블 생성시 겹치는 셀은 두줄로 나옵니다.&#x20;

border-collapse : collapse; 를 설정하면 한줄로 나옵니다.&#x20;



### 만약 셀 너비를 10px로 3개 만들었다면&#x20;

<figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

* 각 셀은 10px로 생성
* 셀 외각으로 각 셀 테두리의 1/2 만큼 영역이 존재. \
  가로 사이즈는 왼쪽 셀 테두리의 1/2 + 오른쪽 셀 테두리의 1/2 이 더해짐.\
  위 표의 가로사이즈는  0.5px 10px 10px 10px 0.5px 이렇게 됨.

### 만약 왼쪽 셀의  왼쪽 테두리를 3px로 변경한다면

<div align="left">

<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

</div>

왼쪽 셀의 사이즈는 변경이 없고,&#x20;

내부테두리가 1.5px, 외부테두리가 1.5px 생기며 전체 사이즈가 2 늘어나게 됨.



### 기껏 html 에서outline, border 만들어 두고, 우리는 닷넷과 달라요 하던놈이  이 테이블 컨트롤에서는 자동으로 반씩 나눠서 계산해버림...

저 셀테두리는 border 속성만 변경한 것.

셀간 겹치는 라인의 경우 더 두꺼운쪽의 속성이 적용됨.





세로..의 경우는 또 다름.

테두리 사이즈가 늘어날경우 가로의 경우 외부반,내부반 해서 위처럼 생성됨.

그러나 세로는 내부영역은 변하지 않고, 외부에 테두리가 추가되고 전체 컨트롤 사이즈가 늘어남.

일관성이 없음. 개판임.



그래서 테두리에 따라서 세로 계산해주자 라고 생각했는데

col row 병합시에 수동으로 영역 지정한건 또 제대로 안먹음.



이럴꺼면 그냥 자체 테이블 컨트롤을 만듬..



### 물론.&#x20;

border-collapse : collapse;  이 속성을 사용하지 않는다면 위의 모든 고민을 할 필요 없이 간단하게 사용하면 됨.&#x20;

모든 문제가 내부 1px로 대충이라도 표시해주자 하는 저 속성 때문에 발생한 문제.
