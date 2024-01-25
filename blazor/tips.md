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

javascript 에서는 당연하지만,&#x20;

blazor 에서는 다른 코드들 처럼 독립적으로 사용할 수 있다고 생각할 수 있음.&#x20;

주의할 것
