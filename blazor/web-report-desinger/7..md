# 용지 설정

눈금자를 만듬으로 인해서 발생된 나비효과가 있습니다.&#x20;

1. 눈금자를 만들었다.
2. 용지 사이즈에 맞게 눈금자를 조절해야 한다.\
   \->   용지 사이즈를 설정 가능해야 한다.&#x20;
3. 브라우저의 사이즈에 따라 용지 정렬도 추가 해야한다.&#x20;



우측 사이드바에 용지 설정 페이지를 포함할 Settings 그룹을 하나 만듭니다. 그리고 그 아이콘을 선택했을때 용지 설정 페이지가 나오도록 수정합니다.&#x20;

용지 설정 페이지를 별도의 컨트롤로 분리시킵니다.&#x20;

{% code title="RightSideBar.razor" %}
```cshtml
<RadzenSidebar @bind-Expanded="@sidebar6Expanded" Style="width: auto; grid-area: rz-right-sidebar">
     //세로로 분할
     <RadzenStack Orientation="Orientation.Horizontal" AlignItems="AlignItems.Stretch" JustifyContent="JustifyContent.SpaceBetween" Style="height:inherit;gap:0">
         //가로로 분할        
         <RadzenStack Orientation="Orientation.Vertical">
+              @if (this.currentRightPanel == RightPanelType.Settings)
+               {
+                    <RadzenPanelMenuItem Text="Paper" Icon="description">
+                        <PaperSetting></PaperSetting>
+                    </RadzenPanelMenuItem>
+               }
         </RadzenStack>
         <RadzenStack Orientation="Orientation.Vertical" Style="border-left: 1px solid lightgray;">
             <RadzenPanelMenu DisplayStyle="MenuItemDisplayStyle.Icon" ShowArrow=false>
                 <RadzenPanelMenuItem Text="Property" Icon="edit_attributes" />
                 <RadzenPanelMenuItem Text="FieldList" Icon="storage"  />
                 <RadzenPanelMenuItem Text="ObjectBrowser" Icon="account_tree" />
+                <RadzenPanelMenuItem Text="Settings" Icon="settings" Click="ShowRightPanel" />
             </RadzenPanelMenu>
         </RadzenStack>
     </RadzenStack>
</RadzenSidebar>

@code{
    enum RightPanelType
    {
        Property,
        FieldList,
        ObjectBrowser,
        Settings
    }
    private RightPanelType currentRightPanel = RightPanelType.Property;
    void ShowRightPanel(MenuItemEventArgs args)
    {
        switch (args.Text.ToUpper())
        {
            case "SETTINGS":
                currentRightPanel = RightPanelType.Settings;
                break;
        }
    }
}
```
{% endcode %}

용지 설정 페이지에는

용지 방향(가로, 세로)

용지 크기

여백 설정

세 종류의 기능이 들어갑니다. 그러나 여백 설정은 아직 진행하지 않습니다. \


{% code title="PaperSetting.razor" %}
```cshtml
@using System.Drawing;
@inject DesignerOptionService Options

<div class="side-panel-contents-background">
    <RadzenText Text="@DD("Direction")" TextStyle="TextStyle.Subtitle2" Style="margin-top:0.5rem;" />
    <RadzenSelectBar @bind-Value=@IsLandScape TValue="bool" class="d-flex" Size="ButtonSize.ExtraSmall" Style="margin:0.25rem 0 0.25rem 0;">
        <Items>
            <RadzenSelectBarItem Text="@DD("Portrait")" Value="false" Style=" width:100%; text-align:center; " />
            <RadzenSelectBarItem Text="@DD("Landscape")" Value="true" Style=" width:100%; text-align:center; " />
        </Items>
    </RadzenSelectBar>
    <RadzenText Text="@DD("Paper Kind")" TextStyle="TextStyle.Subtitle2" Style="margin-top:0.5rem;" />
    <RadzenDropDown AllowClear="false" AllowFiltering="false" @bind-Value=@PaperKindValue Data=@paperKinds ValueProperty="Name" Style=@dropdownStyle PopupStyle=@popupStyle >
        <Template>
            @(context.Name)
        </Template>
    </RadzenDropDown>
    
    <RadzenText Text="@DD("Margin")" TextStyle="TextStyle.Subtitle2" Style="margin-top:0.5rem;" />
    <RadzenSelectBar @bind-Value=@marginType TValue="int" class="d-flex" Size="ButtonSize.ExtraSmall" Style="margin:0.25rem 0 0.25rem 0;">
        <Items> 
            <RadzenSelectBarItem Text="@DD("Default")" Value="0" Style=" width:100%; text-align:center; " />
            <RadzenSelectBarItem Text="@DD("None")" Value="1" Style=" width:100%; text-align:center; " />
            <RadzenSelectBarItem Text="@DD("Custom")" Value="2" Style=" width:100%; text-align:center; " />
        </Items>
    </RadzenSelectBar>
</div>
<style>
    .side-panel-contents-background {
        padding: 0.75rem 1.25rem 0.75rem 1.25rem;
        background-color: var(--rz-panel-menu-item-background-color);
    }
</style>
@code {
    private string dropdownStyle = @"display:block;padding:5px 3px 3px 7px;
                                    --rz-input-height:1.8rem;
                                    --rz-input-font-size:0.7rem;";
    /// <summary>
    /// 이런 변수는 HotReload시 적용되지 않는다. 불러오는 부분이 없어서 그런듯 하다.
    /// </summary>
    private string popupStyle = @"--rz-dropdown-item-font-size:0.7rem; 
                                  --rz-dropdown-item-padding: 0.4rem 0.5rem;";
    private bool isLandscape = false;
    public bool IsLandScape
    {
        get { return this.isLandscape; }
        set { this.isLandscape = value;
            Options.IsLandscape = value;
            ChangePaperSize();
        }
    }

    private int marginType = 0;

    //추후 다국어 처리를 위한 함수
    string DD(string text)
    {
        // if (this.initialized)
        //     text = LanguageManager.Instance.DD(text);
        return text;
    }
    string paperKind = "A4";
    List<PaperKind> paperKinds = new List<PaperKind>();
    public string PaperKindValue
    {
        get{ return this.paperKind; }
        set{ this.paperKind = value;
            ChangePaperSize();
        }
    }

   

    protected override async Task OnInitializedAsync()
    {
        await base.OnInitializedAsync();
        //96dpi 기준일때의 값
        if (paperKinds.Count == 0)
        {
            AddPaperKinds();
        }
    }

    private void AddPaperKinds()
    {
        paperKinds.Add(new PaperKind("A3", 1129, 1596));
        paperKinds.Add(new PaperKind("A4", 798, 1123));
        paperKinds.Add(new PaperKind("A5", 562, 798));
        paperKinds.Add(new PaperKind("B4", 977, 1383));
        paperKinds.Add(new PaperKind("B5", 692, 977));
        paperKinds.Add(new PaperKind("Letter", 821, 1062));
    }

    private void ChangePaperSize()
    {
        Size paperSize;

        if (paperKinds.Count == 0)
        {
            AddPaperKinds();
        }
        PaperKind? paper = paperKinds.Find(x => x.Name == this.paperKind);
        if(paper is not null)
        {            
            if(this.isLandscape == false)
            {
                paperSize = new Size(paper.Width, paper.Height);
            }
            else
            {
                paperSize = new Size(paper.Height, paper.Width);
            }
            Options.PaperSize = paperSize;
        }
    }

    private class PaperKind
    {
        public string Name { get; set; }
        public int Width { get; set; }
        public int Height { get; set; }

        public PaperKind(string name, int width, int height)
        {
            this.Name = name;
            this.Width = width;
            this.Height = height;
        }
    }
}

```
{% endcode %}



용지 정보 같은 기능은 저 페이지에서만 사용되는 것이 아니라 전반적으로 디자이너에서 사용되는 정보 입니다.&#x20;

그러므로 서비스(싱글톤)로 어디서든지 접근가능하게 만드는 것이 좋습니다.\
(기존 C#에서는  static 클래스나 싱글톤으로 불렀던것을  Blazor 에서는 서비스라고 부르나 봅니다.)



{% code title="DesignerOptionService" %}
```csharp

using Microsoft.Extensions.Options;
using System.Drawing;

namespace ReportDesigner.Blazor.Common.Services
{
    public class DesignerOptionService
    {
        public Margin PaperMargin { get; set; } = new Margin();

        public bool IsLandscape { get; set; } = false;

        private Size paperSize = new Size(798, 1124);

        public Size PaperSize { 
            get { return this.paperSize; }
            set { this.paperSize = value;
                PaperSizeChanged?.Invoke(paperSize.Width, new EventArgs());
            }
        }

        public  event EventHandler<EventArgs>? PaperSizeChanged;
    }

    public class Margin
    {
        public int Left { get; set; } = 10;
        public int Top { get; set; } = 10;
        public int Right { get; set; } = 10;
        public int Bottom { get; set; } = 10;
    }
}
```
{% endcode %}

메인 프로젝트에 추가( Client, Maui 등에 따라서 등록위치가 달라짐)

{% code title="Program.cs" %}
```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddRazorPages();
builder.Services.AddServerSideBlazor();
+ builder.Services.AddScoped<DesignerOptionService>(); //추가
var app = builder.Build();
```
{% endcode %}



눈금자와 용지설정값을 Body 부분에 적용시키겠습니다.&#x20;

* 눈금자의 사이즈는 용지사이즈로 설정하며,
* 리포트 배경사이즈 또한 용지사이즈로 설정합니다.&#x20;
* 2023.10 월 현재 div의 사이즈를 가져오는 Blazor 컨트롤을 찾을수 없습니다. 그러므로 javascript를 통해서 가져옵니다.&#x20;
* 상단은 항상 위로 붙이고(세로 가운데 정렬은 안함) 가로 정렬의 경우 Left-margin을 설정하여 변경합니다.&#x20;
* 브라우저 사이즈변경 이벤트또한 Blazor 용 컨트롤을 찾을 수 없기 때문에 JS를 통하여 수행합니다. \
  Blazor에서 호출해서 가져오는것이 아니기 때문에 JS에서 Blazor로 값을 넘길 방법이 필요합니다.&#x20;



JS에서 Blazor로 값을 넘기는 방법([https://learn.microsoft.com/ko-kr/aspnet/core/blazor/javascript-interoperability/call-dotnet-from-javascript?view=aspnetcore-7.0](https://learn.microsoft.com/ko-kr/aspnet/core/blazor/javascript-interoperability/call-dotnet-from-javascript?view=aspnetcore-7.0))

1. Static 함수로 넘기는 방법\
   \-> 간단하지만 Static 함수로 넘기기 때문에 독립적인 로직이 아니면 일반 변수 및 함수 호출의 불편
2. Blazor에의 객체를 한번 넘기고, 그 객체를 통해서 JS에서 Blazor로 호출하는 방법\
   \-> 초반에 객체를 넘겨야 하지만, 한번 넘겨두면 전역변수처럼 두고두고 쓸 수 있음.&#x20;

브라우저의 사이즈를 가져와서 그 값으로 Body안의 컨트롤 사이즈를 변경 시켜야 하기 때문에 2번의 방법으로 사용하겠습니다.&#x20;

1번이 불가능한 건 아니지만 모든 결정은 코드 구현이 단순한 방향으로 정했습니다.&#x20;



{% code title="Body.razor" %}
```cshtml
@page "/body"
@using Microsoft.JSInterop;
@inject DesignerOptionService Options
@inject IJSRuntime JsRuntime

<RadzenBody id="MainBody">
    <div class="report-background"
         style=@("transform:scale(" + designer.Zoom * 0.01f + ");margin:16px 0 0 " + leftOffset + "px;")>
        <RulerX Size="@Options.PaperSize.Width"></RulerX>
        <RulerY Size="@Options.PaperSize.Height"></RulerY>
        <Report Width="@Options.PaperSize.Width" Height="@Options.PaperSize.Height" />
    </div>
</RadzenBody>
<script>
    window.getBodyWidth = function () {
        return document.getElementById('MainBody').offsetWidth;
    };
</script>
<style>
    .report-background{
        transform-origin:top;
        position:absolute;
        top:0%;
        left:50%;
        /* report-background 가로사이즈의 절반 = 용지사이즈 + 좌측눈금자의 반 */
        transform: scale(1);
        padding:25px;
    }  
</style>
@code {

    [CascadingParameter]
    private Designer designer { get; set; }
    private int leftOffset = 0;
    protected override async Task OnInitializedAsync()
    {
        base.OnInitialized();
        Options.PaperSizeChanged += this.PaperSizeChanged;

        //todo 용지 사이즈를 초반에 셋팅해주는 부분이지만 이런코드를 넣는 위치를 바꿀 필요가 있다. 
        PaperSizeChanged(Options.PaperSize.Width, null);
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        //자바스크립트에서 .Net을 호출하기 위한 객체 전달
        var lDotNetReference = DotNetObjectReference.Create(this);
        await JsRuntime.InvokeVoidAsync("GLOBAL.SetDotnetReference", lDotNetReference);

        //브라우저 사이즈시 콜백 추가
        await JsRuntime.InvokeAsync<object>("browserResize.registerResizeCallback");
    }
    [JSInvokable]
    public void OnBrowserResize()
    {
        PaperSizeChanged(Options.PaperSize.Width, null);
    }


    private void PaperSizeChanged(object? sender, EventArgs? args)
    {
        Task.Factory.StartNew(async () =>
        {
            int paperWidth = (int)sender;
            int bodyWidth = await JsRuntime.InvokeAsync<int>("getBodyWidth");        
            //todo 제대로된 계산할 필요가 있음.

            if (bodyWidth  > paperWidth)
                leftOffset = - (25 + paperWidth) / 2;
            else
                leftOffset = -(bodyWidth / 2) + 16;

            await this.InvokeAsync(StateHasChanged);
        });        
    }
}

```
{% endcode %}

{% code title="browser-resize.js" %}
```javascript
var GLOBAL = {};
GLOBAL.DotNetReference = null;
GLOBAL.SetDotnetReference = function (pDotNetReference) {
    GLOBAL.DotNetReference = pDotNetReference;
};

window.browserResize = {
    getInnerHeight: function () {
        return window.innerHeight;
    },
    getInnerWidth: function () {
        return window.innerWidth;
    },
    registerResizeCallback: function () {
        window.addEventListener("resize", browserResize.resized);
    },
    resized: function () {
        //1.Static 함수를 호출하는 방법
        //DotNet.invokeMethodAsync('{ASSEMBLY NAME}', '{.NET METHOD ID}', {ARGUMENTS});
        /* DotNet.invokeMethodAsync('ReportDesigner.Blazor.Common', 'OnBrowserResize').then(data => data);*/

        //2.Blazor 객체를 통하여 호출하는 방법
        GLOBAL.DotNetReference.invokeMethodAsync('OnBrowserResize');
    }
}
```
{% endcode %}
