# 06. Snap Control

1. 현재 영역 안의 모든 컨트롤의 모서리 좌표 (x,y,right,bottom) 를 가져온다.
2. 드래그 중에 드래그 오브젝트가 모서리좌표의 근처에 있을 경우 Snap Line을 표시한다.
3. 드래그 중인 오브젝트가 Snap Point에 많이 가까워지면 드래그 중인 오브젝트의 위치를 Snap Point로 이동시킨다.

여기서  문제점은 01 에서 만든 드래그 컨트롤은  HTML 기본 요소로 사용했기 때문에, 드래그 중인 요소의 위치를 강제로 이동이 시키는 것이 불가능합니다. &#x20;

즉 우리는 새로운 드래그 컨트롤을 만들어서 수동으로 이동시켜야 합니다.&#x20;

<details>

<summary>Page.razor</summary>

```cshtml
@page "/06"
<h3>06. Snap</h3>

<_06_Band></_06_Band>
<style>
    .band {
        background-color: aliceblue;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }

    .drag-object {
        position: absolute;
        border: 1px solid green;
    }
   
    .dragable-area {
        height: 500px;
        border: 1px dashed black;
        position: relative;
    }

</style>
@code {

}

```

</details>

<details>

<summary>Band.razor</summary>

```cshtml
@using System.Numerics;
@inject _06_DragAndDropService DragService
<div class="dragable-area band"
     @onpointerdown=OnPointerDown     @onpointerup=OnPointerUp
     @onpointermove=OnPointerMove     ondragover="event.preventDefault()">
    <CascadingValue Value="this">
        @foreach (_06_BaseControlModel control in Controls)
        {
            <_06_BaseControl BaseControlModel="control"></_06_BaseControl>
        }
    </CascadingValue> 
</div>

@code {
    public List<_06_BaseControlModel> Controls { get; set; } = new List<_06_BaseControlModel>();

    List<int> snapX = new List<int>();
    List<int> snapY = new List<int>();

    private _06_BaseControlModel? CurrentSelectedModel;

    public void SetCurrentControl(string uid)
    {
        CurrentSelectedModel = Controls.Find(x => x.Uid == uid);

        if (CurrentSelectedModel is not null)
        {
            DragService.Hidden = false;
            DragService.X = CurrentSelectedModel.X;
            DragService.Y = CurrentSelectedModel.Y;
            DragService.Width = CurrentSelectedModel.Width;
            DragService.Height = CurrentSelectedModel.Height;
        }
    }   

    void OnPointerDown(PointerEventArgs e)
    {
        //마우스의 Client의 절대좌표에서 컨트롤안에서의 마우스상대좌표를 빼면 현재 컨트롤의 Client좌표를 얻을 수 있다.
        if (CurrentSelectedModel is not null && CurrentSelectedModel.DragAble)
            DragService.StartDrag(CurrentSelectedModel.X, CurrentSelectedModel.Y, e.ClientX, e.ClientY);
    }
    void OnPointerMove(PointerEventArgs e)
    {
        if (CurrentSelectedModel is not null && CurrentSelectedModel.DragAble)
        {
            DragService.Move(e.ClientX, e.ClientY);
        }
    }
    void OnPointerUp(PointerEventArgs e)
    {

        if (CurrentSelectedModel is not null)
        {
            if (CurrentSelectedModel.DragAble)
            {
                CurrentSelectedModel.X = DragService.X;
                CurrentSelectedModel.Y = DragService.Y;
                DragService.Hidden = true;
            }
            CurrentSelectedModel.DragAble = false;

        }
    }
}

```

</details>

드래그 중인 오브젝트의 위치를 계속 업데이트 해야하기 때문에, 현재 선택한 오브젝트를 멤버변수로 갖고 있도록 변경하였습니다.&#x20;

<details>

<summary>BaseControl.razor</summary>

```cshtml
@inject _06_DragAndDropService DragService

<div class="componentbase"
     @onpointerdown=OnPointerDown
     style=@("transform:" + GetStyle("translate") + "position:absolute;" + GetStyle("width") + GetStyle("height") + GetStyle("border"))>
</div>

@code {
    [CascadingParameter] _06_Band Band { get; set; }
    [Parameter] public _06_BaseControlModel BaseControlModel { get; set; } = new _06_BaseControlModel();

    void OnPointerDown(PointerEventArgs e)
    {
        BaseControlModel.Selected = true;
        BaseControlModel.DragAble = true;
        Band.SetCurrentControl(BaseControlModel.Uid);
        Console.WriteLine($"componentbase OnPointerDown {(int)e.OffsetX} ");
    }
    public string GetStyle(string type)
    {
        switch (type.ToLower())
        {
            case "translate":
                return "translate(" + BaseControlModel.X + "px, " + BaseControlModel.Y + "px);";
            case "width":
                return "width:" + BaseControlModel.Width + "px;";
            case "height":
                return "height:" + BaseControlModel.Height + "px;";
            case "border":
                if (BaseControlModel.Border)
                    return "border: 1px solid grey;";
                else
                    return string.Empty;
            default:
                return string.Empty;
        }
    }
}

```

</details>

<details>

<summary>BaseControlModel.cs</summary>

```csharp
public class _06_BaseControlModel
{
    public int TabIndex { get; set; } = 0;
    public string Uid { get; set; } = string.Empty;

    public int Width { get; set; } = 100;

    public int Height { get; set; } = 30;

    public double X { get; set; } = 0;

    public double Y { get; set; } = 0;

    public int Right => (int)X + Width;

    public int Bottom => (int)Y + Height;

    public bool Border { get; set; } = true;

    private bool selected = false;
    public bool Selected
    {
        get { return this.selected; }
        set
        {
            this.selected = value;           
        }
    }

    public bool DragAble { get; set; } = false;
}



```

</details>

<details>

<summary>DragObject.razor</summary>

```cshtml
<div class="drag-object"
     style=@("width:" + DragService.Width + "px;height:" + DragService.Height + "px;" +
             "transform:translate(" + (DragService.X) + "px, " + (DragService.Y) + "px);") hidden=@DragService.Hidden>
</div>

@code {
    //서비스를 Inject해서 사용하지 않고, 파라메터로 사용하는 이유는 UI 갱신이 때문이다.
    //Blazor에서 사용하는 태그는 데이터가 변경된다고 UI가 갱신되지 않고,
    //Blazor의 속성이나 렌더를 수동으로 변경해야 발생한다. 
    [Parameter]
    public _06_DragAndDropService DragService { get; set; } = new _06_DragAndDropService();
}

```

</details>

<details>

<summary>DragAndDropService.cs</summary>

```csharp
public class _06_DragAndDropService
{

    public int Width { get; set; } = 100;

    public int Height { get; set; } = 30;
    public int Right => (int)X + Width;

    public int Bottom => (int)Y + Height;

    public double X { get; set; } = 0;

    public double Y { get; set; } = 0;

    public bool Hidden { get; set; } = true;

    private double diffX;
    private double diffY;
    public void StartDrag(double objectPosX, double objectPosY, double mouseClientX, double mouseClientY)
    {
        diffX = mouseClientX - objectPosX;
        diffY = mouseClientY - objectPosY;
    }

    public void Move(double mouseClientX, double mouseClientY)
    {
        X = mouseClientX - diffX;
        Y = mouseClientY - diffY;
    }
}

```

</details>

이걸로 01에서 구현했던 Drag And Drop 컨트롤을 별도로 만들었습니다.&#x20;



<details>

<summary>SnapLine.razor</summary>

```cshtml
<div style="position:absolute;width:100%;height:100%">
    <div class="snap-vertical" style=@(GetTransform("left")) hidden=@Model.LeftHidden></div>
    <div class="snap-horizontal" style=@(GetTransform("top")) hidden=@Model.TopHidden></div>
    <div class="snap-vertical" style=@(GetTransform("right")) hidden=@Model.RightHidden></div>
    <div class="snap-horizontal" style=@(GetTransform("bottom")) hidden=@Model.BottomHidden></div>
</div>
<style>
 
    .snap-vertical {
        height: 100%;
        width:0px;
        border-left: solid 1px red;
        position:absolute;
    }

    .snap-horizontal {
        width: 100%;
        height:0px;
        border-top: solid 1px red;
        position: absolute;
    }

</style>

@code{

    [Parameter]
    public _06_SnapLineModel Model { get; set; } = new _06_SnapLineModel();

    public string GetTransform(string direction)
    {
        string rValue = string.Empty;
        switch (direction)
        {
            case "left":
                rValue= "transform:translate(" + Model.LeftPos + "px,0px);"; break;
            case "top":
                rValue = "transform:translate(0px," + Model.TopPos + "px);"; break;
            case "right":
                rValue = "transform:translate(" + Model.RightPos + "px,0px);"; break;
            case "bottom":
                rValue = "transform:translate(0px," + Model.BottomPos + "px); "; break;
        }
        return rValue;
    }
}
```

</details>

<details>

<summary>SnapLineModel.cs</summary>

```csharp
using Microsoft.AspNetCore.Components;

public class _06_SnapLineModel
{
    public bool LeftHidden { get; set; } = true;
    public bool TopHidden { get; set; } = true;
    public bool RightHidden { get; set; } = true;
    public bool BottomHidden { get; set; } = true;
    public int LeftPos { get; set; }
    public int TopPos { get; set; }
    public int RightPos { get; set; }
    public int BottomPos { get; set; }

    public void HideSnapLine()
    {
        LeftHidden= true;
        TopHidden= true;
        RightHidden= true;
        BottomHidden= true;
    }

    public void ShowSnapLine(string direction, bool isShowSnapLine, int value)
    {
        switch (direction)
        {
            case "left":
                LeftHidden = !isShowSnapLine;
                LeftPos = value;
                break;
            case "top":
                TopHidden = !isShowSnapLine;
                TopPos = value;
                break;
            case "right":
                RightHidden = !isShowSnapLine;
                RightPos = value;
                break;
            case "bottom":
                BottomHidden = !isShowSnapLine;
                BottomPos = value;
                break;
        }
    }
}

```

</details>

테스트를 위해서 2개의 컨트롤을 임의의 위치에 생성하고, 위치정보를 리스트로 저장합니다.

드롭(포인트업) 이벤트가 발생시 다시 위치정보를 갱신 시킵니다.&#x20;

<details>

<summary>Snap 추가된 Band.razor</summary>

```cshtml
@using System.Numerics;
@inject _06_DragAndDropService DragService

<div class="dragable-area band"
     @onpointerdown=OnPointerDown
     @onpointerup=OnPointerUp
     @onpointermove=OnPointerMove
     ondragover="event.preventDefault()">
    <_06_SnapLine Model="SnapLineModel"></_06_SnapLine>

    <CascadingValue Value="this">
        @foreach (_06_BaseControlModel control in Controls)
        {
            <_06_BaseControl BaseControlModel="control"></_06_BaseControl>
        }
    </CascadingValue> 

    <_06_DragObject DragService="DragService"></_06_DragObject>
</div>

@code {
    public List<_06_BaseControlModel> Controls { get; set; } = new List<_06_BaseControlModel>();


    List<int> snapX = new List<int>();
    List<int> snapY = new List<int>();

    public _06_SnapLineModel SnapLineModel { get; set; } = new _06_SnapLineModel();

    private _06_BaseControlModel? CurrentSelectedModel;

    public void SetCurrentControl(string uid)
    {
        CurrentSelectedModel = Controls.Find(x => x.Uid == uid);

        if (CurrentSelectedModel is not null)
        {
            DragService.Hidden = false;
            DragService.X = CurrentSelectedModel.X;
            DragService.Y = CurrentSelectedModel.Y;
            DragService.Width = CurrentSelectedModel.Width;
            DragService.Height = CurrentSelectedModel.Height;
        }
    }
    protected override void OnInitialized()
    {
        _06_BaseControlModel control = new _06_BaseControlModel()
            {
                Uid = Guid.NewGuid().ToString(),
                X = 10,
                Y = 20,
                Width = 200,
                Height = 50
            };

        //컴포넌트 목록에 추가한다.
        this.Controls.Add(control);

        control = new _06_BaseControlModel()
            {
                Uid = Guid.NewGuid().ToString(),
                X = 40,
                Y = 90,
                Width = 50,
                Height = 150
            };

        ////컴포넌트 목록에 추가한다.
        this.Controls.Add(control);
        UpdateSnapControl();
    }   

    void OnPointerDown(PointerEventArgs e)
    {
        //마우스의 Client의 절대좌표에서 컨트롤안에서의 마우스상대좌표를 빼면 현재 컨트롤의 Client좌표를 얻을 수 있다.
        if (CurrentSelectedModel is not null && CurrentSelectedModel.DragAble)
            DragService.StartDrag(CurrentSelectedModel.X, CurrentSelectedModel.Y, e.ClientX, e.ClientY);

    }
    void OnPointerMove(PointerEventArgs e)
    {
        if (CurrentSelectedModel is not null && CurrentSelectedModel.DragAble)
        {
            DragService.Move(e.ClientX, e.ClientY);

            DoSnap();
        }
    }
    void OnPointerUp(PointerEventArgs e)
    {

        if (CurrentSelectedModel is not null)
        {
            if (CurrentSelectedModel.DragAble)
            {
                CurrentSelectedModel.X = DragService.X;
                CurrentSelectedModel.Y = DragService.Y;
                DragService.Hidden = true;
            }
            CurrentSelectedModel.DragAble = false;
            SnapLineModel.HideSnapLine();
            UpdateSnapControl();
        }
    }
    /// <summary>
    /// 현재 밴드에 포함된 모든 컨트롤의 스냅 위치를 계산한다.
    /// 1. 컨트롤 생성, 삭제
    /// 2. 컨트롤 이동
    /// 3. 컨트롤 사이즈 변경
    /// </summary>
    public void UpdateSnapControl()
    {
        snapX.Clear();
        snapY.Clear();

        snapX.Add(0);
        snapY.Add(0);

        //리포트 사이즈를 넣는다 x,y는 0일테니 의미없고,

        //밴드 사이즈를 넣는다. 

        //리포트 사이즈를 넣는다.

        foreach (_06_BaseControlModel control in Controls)
        {
            int x = (int)control.X;
            int y = (int)control.Y;
            int right = (int)control.Right;
            int bottom = (int)control.Bottom;
            if (snapX.Contains(x) == false)
                snapX.Add(x);
            if (snapY.Contains(bottom) == false)
                snapY.Add(bottom);
            if (snapY.Contains(y) == false)
                snapY.Add(y);
            if (snapX.Contains(right) == false)
                snapX.Add(right);
        }
        string strx = String.Join(", ", snapX.ToArray());
        string stry = String.Join(", ", snapY.ToArray());
        Console.WriteLine($"UpdateSnapControl x:[{strx}] y:[{stry}]");
    }

   

    private void DoSnap()
    {
        Dictionary<string, int> dragObjectSnapPoint = new Dictionary<string, int>();
        dragObjectSnapPoint.Add("left", (int)DragService.X);
        dragObjectSnapPoint.Add("top", (int)DragService.Y);
        dragObjectSnapPoint.Add("right", (int)DragService.Right);
        dragObjectSnapPoint.Add("bottom", (int)DragService.Bottom);

        foreach (KeyValuePair<string, int> point in dragObjectSnapPoint)
        {
            int findValue = point.Value;
            int value = 0;

            if (point.Key.Equals("left") || point.Key.Equals("right"))
                value = snapX.OrderBy(x => Math.Abs(findValue - x)).First();
            else
                value = snapY.OrderBy(x => Math.Abs(findValue - x)).First();

            if (Math.Abs(findValue - value) < 10)
            {
                SnapLineModel.ShowSnapLine(point.Key, true, value);
                if (Math.Abs(findValue - value) < 5)
                {
                    SetSnap(point.Key, value);
                }
            }
            else
            {
                SnapLineModel.ShowSnapLine(point.Key, false, value);
            }
        }
      
        void SetSnap(string direction, int value)
        {
            switch (direction)
            {
                case "left":
                    DragService.X = value;
                    break;
                case "top":
                    DragService.Y = value;
                    break;
                case "right":
                    DragService.X = value - (int)DragService.Width;
                    break;
                case "bottom":
                    DragService.Y = value - (int)DragService.Height;
                    break;
            }
        }
    }
   
}

```

</details>
