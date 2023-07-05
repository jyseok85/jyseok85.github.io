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
