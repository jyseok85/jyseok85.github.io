2022.11.22
테마추가
1. Radzen 테마를 가져온다.(깃이나, 빌드디렉토리)
2. 프로젝트의 wwwroot에 복사한다. 
3. index.html에서 기존 기본경로의 테마를 사용하던것을 새로추가한 경로로 변경한다.
4. 테마를 입맛에 맞게 수정한다. 

RadzenDropDwon에 국가별 이미지 추가능기능
- 기본클래스

``` c#
public class LanguageManager
{
    private LanguageManager() { }
    //private static 인스턴스 객체
    private static readonly Lazy<LanguageManager> _instance = new Lazy<LanguageManager>(() => new LanguageManager());
    //public static 의 객체반환 함수
    public static LanguageManager Instance { get { return _instance.Value; } }

    private List<LanguageInfo> languageInfos = new List<LanguageInfo>();
    public List<LanguageInfo> Languages
    {
        get
        {
            if (this.languageInfos.Count == 0)
            {
                languageInfos.Add(new LanguageInfo("EN", "imagePath or imageData"));
                languageInfos.Add(new LanguageInfo("KR", "imagePath or imageData"));
                languageInfos.Add(new LanguageInfo("JP", "imagePath or imageData"));
                languageInfos.Add(new LanguageInfo("CN", "imagePath or imageData"));
            }
            return languageInfos;
        }
     }
}
        
public class LanguageInfo
{ 
    public string IconImage { get; set; }
    public string NationalCode { get; set; }
    public LanguageInfo(string nationalCode , string iconImage)
    {
        this.IconImage= iconImage;
        this.NationalCode = nationalCode;
    }
}
```

- 이미지 표시를 위한 드롭다운박스 수정
``` razor
<RadzenDropDown AllowClear="false"                                              <!--검색기능 제거 -->
                TValue="string"                
                Data=@LanguageManager.Instance.Languages                        <!-- 데이터는 해당 클래스의 리스트 -->
                TextProperty="@nameof(LanguageInfo.NationalCode)"               <!-- 데이터의 표시할 값 -->
                Change=@(args => OnChange(args))>                               <!-- 드롭다운박스 변경 이벤트 -->
        <Template Context="dropDownContext">                                    <!-- 템플릿을 추가해서 오버라이드 한다. -->
        @{
                var context = dropDownContext as LanguageInfo;                  <!-- 드롭다운의 아이템을 해당 클래스 객체로 변환 -->
                <RadzenImage Path=@(context.IconImage) />                       <!-- RadzenImage 표시 -->
                @context.NationalCode                                           <!-- 텍스트 표시-->
        }
        </Template>
</RadzenDropDown>
```

- 실제 데이터 바인딩처리
``` razor
<RadzenDropDown AllowClear="false"                                              <!--검색기능 제거 -->
                TValue="string"                
                Data=@LanguageManager.Instance.Languages                        <!-- 데이터는 해당 클래스의 리스트 -->
                TextProperty="@nameof(LanguageInfo.NationalCode)"               <!-- 데이터의 표시할 값 -->
(+)             ValueProperty="@nameof(LanguageInfo.NationalCode)"              <!-- 데이터의 값 타입 -->
(+)             @bind-Value="LanguageManager.Instance.CurrentLanguage"          <!-- 실제 바인딩될 객체 -->
                Change=@(args => OnChange(args))>                               <!-- 드롭다운박스 변경 이벤트 -->       
        <Template Context="dropDownContext">                                    <!-- 템플릿을 추가해서 오버라이드 한다. -->
        @{
                var context = dropDownContext as LanguageInfo;                  <!-- 드롭다운의 아이템을 해당 클래스 객체로 변환 -->
                <RadzenImage Path=@(context.IconImage) />                       <!-- RadzenImage 표시 -->
                @context.NationalCode                                           <!-- 텍스트 표시-->
        }
        </Template>
</RadzenDropDown>
```
~~@bind-Value vs Value
@bind-Value 의 경우 실시간 값이 적용됨 - 드롭다운에서 값을 바꾸면 바로 적용되고, 이벤트에서 확인시 해당 변수의 값이 바뀐걸 확인
Value는 값을 수동으로 변경해야함
OnChange 이벤트 내에서 값을 재조정해야 할 경우에는 Value를 사용하고, 그 이외의 경우는 @bind-Value를 사용해도 무관~~
@bind-Value 로 정상동작확인.


### Radzen 컴포넌트의 문제일수도 있지만 패널 컴포넌트를 빠르게 온오프 할경우 하위 컴포넌트가 선택되는 문제가 있다.

다행히도 bootstrap 에 선택금지 속성이 있으니

패널컴포넌트 부모쪽에 user-select-none 클래스 속성을 지정한다. 


### RadzenDialog가 나오지 않는 문제
다이얼로그를 생성할때?? 모든컨트롤을 생성할때 2가지 방법이 있다.
1. 만들어두고 안보이게 한다.
2. 필요할때만든다.
RadzenDialog 의 경우 프레임워크를 생성해두고 필요할때 불러오는 개념이다. 

그렇기 때문에 MainLayout.razor 에 다이얼로그 컨트롤을 추가해 놓고,
``` razor
<RazdanDialog /> 를 추가
```
다른 페이지에서 사용한다. 

### StateHasChanged(); vs InvokeAsync(StateHasChanged);
보통 Blazor에서 UI를 갱신할때 사용한다.

같은 기능을 하는데 뭘 사용하는게 더 좋은건지 고민을 할 수 있을텐데

그냥 생각없이 
- WASM 은 무조건 StateHasChange();
- ServerSide는 await InvokeAsync(StateHasChanged);
를 사용하면 된다. 

어차피 WASM은 싱글스레드로 동작하기 때문에 비동기가 의미가 없을테고,

서버는 왠만하면 비동기로 만들어야 하니까 일단 await 로 시작한다. (물론 필요에따라서 await를 제거하고 그냥 호출할수도 있다)


오류 로그를 적기 위해서 StackFrame 을 사용해서 메소드, 클래스명을 가져왔다.
그러나 AOT 모드에서는 작동하지 않는다. 
더 문제는 저 StatckFrame 때문이라는걸 밝혀내는데 일주일이 넘게 걸렸다..


### 새창링크열면 기존페이지가 먹통이 되는 현상
개발자도구 창을 실행하면 풀림
검색결과 브라우저와 닷넷과의 버그이며, 
2년전에 닷넷 5버전 후반쯤에 해결되었다는 글이 있지만.
현재 닷넷 7.0.1에서 작업중인데 동일 오류가 있음.

참고로 디버깅시에만 발생하므로 발생하면 개발자도구실행해서 풀고 진행
