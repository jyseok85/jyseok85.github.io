# Html Table 테두리!

기본적으로 테이블 생성시 겹치는 셀은 두줄로 나옵니다.&#x20;

border-collapse : collapse; 를 설정하면 한줄로 나옵니다.&#x20;



### 만약 셀 너비를 10px로 3개 만들었다면&#x20;

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

* 각 셀은 10px로 생성
* 셀 외각으로 각 셀 테두리의 1/2 만큼 영역이 존재. \
  가로 사이즈는 왼쪽 셀 테두리의 1/2 + 오른쪽 셀 테두리의 1/2 이 더해짐.\
  위 표의 가로사이즈는  0.5px 10px 10px 10px 0.5px 이렇게 됨.

### 만약 왼쪽 셀의  왼쪽 테두리를 3px로 변경한다면

<div align="left">

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

</div>

왼쪽 셀의 사이즈는 변경이 없고,&#x20;

내부테두리가 1.5px, 외부테두리가 1.5px 생기며 전체 사이즈가 2 늘어나게 됨.



### 기껏 html 에서outline, border 만들어 두고, 우리는 닷넷과 달라요 하던놈이  이 테이블 컨트롤에서는 자동으로 반씩 나눠서 계산해버림...

저 셀테두리는 border 속성만 변경한 것.

셀간 겹치는 라인의 경우 더 두꺼운쪽의 속성이 적용됨.
