# 테이블 사이즈 조절

테이블 높이를 조절할때 특정 영역 이하로 줄어들지 않는 문제

\-> table 내부 셀의 padding 때문.



드래그 가능한 컨트롤을 사용할때 드래그 도중 이벤트가 끊기는 현상이 있다.

아래의 예제처럼 드래그를 위해 기본 이벤트를 막고 별도의 서비스로 드래그를 만들었을 경우, 순간적으로 끊기는 현상이 있다.&#x20;

```
<div @ondragover:preventDefault="true"
@ondragover:stopPropagation="true"
@ondrag:preventDefault="true"
@ondrag:stopPropagation="true"
@ondragstart:preventDefault="true"
@ondragstart:stopPropagation="true"
@onpointermove:stopPropagation="true"
@onpointermove:preventDefault="true"  
@ondragleave:preventDefault="true"
@ondragleave:stopPropagation="true"
@ondragend:preventDefault="true"
@ondragend:stopPropagation="true"
@ondragenter:preventDefault="true"
@ondragenter:stopPropagation="true"
@onpointerdown:stopPropagation="true" 
@onpointerdown='e=>OnColumnResizePointerDown(e,index)/>
```

원인은 @onpointerdown:preventDefault="true" 이 없어서 기본 이벤트가 실행되었기 때문에 발생했다.&#x20;



Blazor의 경우 글로벌로 이벤트를 다 넣어버리기 때문에 제한할 일이 있을 경우에 확실히 다 해야한다.&#x20;

