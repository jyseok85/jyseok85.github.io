

특정부분만 별도 파일로 분리시키기

일단 Razor 를 만들고 분리시킬 코드를 넣는다.
SubRazor.razor

부모쪽에서 해당 Razor를 생성한다.

SubRazor subRazor

<SubRazor @ref=subRazor/>

부모쪽에서 언제든지 subRazor의 Public으로 설정된 값을 가져 올 수 있다. 

부모를 넘기는 방법은 CascadingValue 를 감싸면 된다.
<CascadingValue Value="this">
  <SubRazor @ref=subRazor/>
</CascadingValue>

[CascadingParameter]
private MainRazor MainRazor { get; set; }
