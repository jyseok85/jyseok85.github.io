# 자식 컨트롤에서 부모 컨트롤 호출 방법

## 1. 파라메터로 부모를 전달.

&#x20;  1 - 1. 명시적으로 다음과 같이 전달할 수도 있고

<pre class="language-cshtml" data-title="ParentComponent.razor"><code class="lang-cshtml">&#x3C;div>
<strong>    &#x3C;ChildComponent Parent="this">&#x3C;/ChildComponent>
</strong>&#x3C;/div>

@code {
   public void Done() {
      // code...

      // Don't forget to call StateHasChanged
      StateHasChanged();
   }
}
</code></pre>

{% code title="ChildComponent.razor" %}
```csharp
[Parameter]
public ParentComponent Parent { get; set; }

protected void DoneButtonClicked()
{
    Parent.Done();
}
```
{% endcode %}

&#x20; 1 - 2.  Blazor에서 제공하는 범용적인 CascadingValue를 통해서 전달도 가능.

<pre class="language-cshtml" data-title="ParentComponent.razor"><code class="lang-cshtml">&#x3C;div>
   &#x3C;CascadingValue Value="this">
    &#x3C;ChildComponent>&#x3C;/ChildComponent>
<strong>   &#x3C;/CascadingValue>
</strong>&#x3C;/div>

@code {
   public void Done() {
      // code...

      // Don't forget to call StateHasChanged
      StateHasChanged();
   }
}
</code></pre>

{% code title="ChildComponent.razor" %}
```csharp
[CascadingParameter]
public ComponentBase Parent { get; set; }
//or 
//public ParentComponent Parent {get;set;}

protected void DoneButtonClicked()
{
    Parent.Done();
}
```
{% endcode %}

## 2. 파라메터로 이벤트콜백을 전달.

{% code title="ParentComponent.razor" %}
```cshtml
<ChildComponent OnClickCallback="@ShowMessage"/>

@code {
    private string message;

    private void ShowMessage(string _message)
    {
        message = _message;
    }
}
```
{% endcode %}

{% code title="ChildComponent.razor" %}
```cshtml
<button @onclick="Call">Parent Call</button>

@code {
       
    [Parameter]
    public EventCallback<string> OnClickCallback {get; set;}
    
    private async Task Call()
    {
        await OnClickCallback.InvokeAsync(message);
    }
}
```
{% endcode %}

