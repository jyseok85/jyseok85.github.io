# Tips

## RenderFragment

부모쪽에서 자식의 element를 (HTML or Razor)  변경하기 위해서 사용.

특정 상황에 대해서 유연한 대처가 가능하지만, 부모쪽에서(해당 컴포넌트를 사용하는)  디자인을 변경하는 케이스라면 소스가깔끔하지 않을 듯 하다.&#x20;

{% code title="Child.Razor" %}
```cshtml
<div>
    @ChildContent
</div>

@code{
    [Parameter]
    public RenderFragment? ChildContent { get; set; }
}
```
{% endcode %}

{% code title="Parent.Razor" %}
```cshtml
<Child>
    <ChildContent> 텍스트나 태그나 기타 등등 </ChildContent>
</Child>
```
{% endcode %}



## Razor 파일에서 스타일 설정시 주의사항

예상 : Razor 컴포넌트 내에서 설정한 스타일이기 때문에 이 컨트롤의 자식 컨트롤에만 영향이 갈것이다.

실제 : 해당 컨트롤이 생성된 이후 모든 스타일이 마지막에 생성한 컨트롤의 스타일로 덮어씌워진다.

ex)a.razor, .razor 은 아래와   같이 동일한 스타일명을 사용함.

<pre data-title="a.razor"><code><strong>&#x3C;div class = "component"/>
</strong><strong>&#x3C;style>
</strong><strong>.component{
</strong>    background-color : red;
}
&#x3C;/style>
</code></pre>

{% code title="b.razor" %}
```
<div class = "component"/>
<style>
.component{
    background-color : blue;
}
</style>
```
{% endcode %}

a 컨트롤을 생성 후 b 컨트롤을 생성하면 b 컨트롤의 스타일로 덮어씌워짐.

javascript 에서는 당연한 코드지만,

blazor 라면 당연히 독립적으로 사용  가능 하다고 생각할 수 있기 때문에 주의해야 함.



실제 html로 변환된 파일을 보면 저 razor 파일내의 스타일은 컨트롤마다 코드가 추가 되기 때문에, 반복적으로 사용될 수 있는 컨트롤이라면 스타일 코드를 제거하는것을 추천.



## 외부 컴포넌트에서 스타일 참조 문제

{% code title="a.razor" %}
```cshtml
<RadzenText class='w-100 custom-class'/>
```
{% endcode %}

{% code title="a.razor.css" %}
```css
.custom-class{
    height:100%
}
```
{% endcode %}

위의 코드는 특별히 이상한 점을 찾을 수 없다.&#x20;

a.razor 파일을 만들고 동일한 네이밍으로 css 를 만들어서&#x20;

해당 css 파일은 a.razor 에서만 사용되도록 처리 되었다.&#x20;



그러나 위 스타일은 적용되지 않는다.&#x20;

blazor에서 컴파일시 생성해 주는 css를 저 외부컴포넌트에서 읽을 방법이 없기 때문이다.&#x20;

기존처럼 별도의 css로 작업하던가, 귀찮으면 a.razor 파일에 스타일을 추가하는 방법을 사용해야 한다.&#x20;





## 비동기로 화면 갱신

StateHasChanged()

를 호출 하면, 화면이 다시 렌더링 된다.&#x20;

그러나 비동기로 호출시 저 코드는 동작하지 않고, 사용자의 액션(보통은 마우스 무브에서 발생)이 있은 후 갱신되게 된다.&#x20;

그럴 경우 저 StateHasChanged() 도 비동기로 호출 하면 정상동작 된다.&#x20;

```csharp
private async void Refresh()
{
    await InvokeAsync(() => {
        StateHasChanged();
    });
}
```

그러나 우리가 원하는건 다음이지 않을까? 추가 되기를 기대한다.&#x20;

```csharp
await StateHasChangedAsync(); 
```
