# 컨트롤 키보드로 이동

너무 당연한 기능이라서 구현 합니다.&#x20;

기능

* 방향키를 누를때 그 방향으로 1px 씩 이동.
* Ctrl + 방향키를 누르면 Snap 포인트로 바로 이동.



컨트롤 조합의 경우

해당 방향의 snap point 가 움직이는 오브젝트의 x1, x2 좌표(왼쪽, 오른쪽  X좌표) 보다 큰 위치를 각각 계산하고, 각각의 좌표에서 가장 가까운 Snap Point로 이동시키면 됩니다.&#x20;

<details>

<summary>Band.razor</summary>

```csharp
private void OnKeyDown(KeyboardEventArgs e)
{
    if (CurrentSelectedModel is not null)
    {
        //컨트롤 키와 같이 누른경우
        if (e.CtrlKey)
        {
            Console.WriteLine($"UpdateSnapControl X: {String.Join(", ", snapX.ToArray())}, Y: {String.Join(", ", snapX.ToArray())}");
            List<int> movement = new List<int>();
            switch (e.Key)
            {
                case "ArrowRight":
                    {
                        //todo
                        //if (CurrentSelectedModel.Right >= 밴드최대사이즈)
                        //break;

                        //현재 right 좌표의 우측에 있는 x좌표를 가져온다. 
                        //컨트롤의 오른쪽 좌표 기준으로 검색을 한다. 
                        var selected1 = snapX.Where(n => n > CurrentSelectedModel.Right);
                        if (selected1.Count() > 0)
                        {
                            var point = selected1.OrderBy(x => x).First();
                            //이동거리를 추가한다. 
                            movement.Add(point - CurrentSelectedModel.Right);
                        }

                        //컨트롤의 왼쪽 좌표 기준으로 오른쪽으로 검색을 한다. 
                        var selected2 = snapX.Where(n => n > CurrentSelectedModel.X);
                        if (selected2.Count() > 0)
                        {
                            var point = selected2.OrderBy(x => x).First();
                            movement.Add(point - (int)CurrentSelectedModel.X);
                        }

                        if (movement.Count > 0)
                        {   //가장 이동이 적은 다음 스냅 포인트로 이동한다.                             
                            CurrentSelectedModel.X += movement.OrderBy(x => x).First();
                        }
                    }
                    break;
                case "ArrowDown":
                    {
                        //todo
                        //if (CurrentSelectedModel.Bottom >= 밴드최대사이즈)
                        //break;
                        var selected1 = snapY.Where(n => n > CurrentSelectedModel.Bottom);
                        if (selected1.Count() > 0)
                        {
                            var point = selected1.OrderBy(x => x).First();
                            //이동거리를 추가한다. 
                            movement.Add(point - CurrentSelectedModel.Bottom);
                        }
                        var selected2 = snapY.Where(n => n > CurrentSelectedModel.Y);
                        if (selected2.Count() > 0)
                        {
                            var point = selected2.OrderBy(x => x).First();
                            movement.Add(point - (int)CurrentSelectedModel.Y);
                        }

                        if (movement.Count > 0)
                        {                               
                            CurrentSelectedModel.Y += movement.OrderBy(x => x).First();
                        }
                    }
                    break;
                case "ArrowLeft":
                    {
                        if (CurrentSelectedModel.X <= 0)
                            break;

                        var selected1 = snapX.Where(n => n < CurrentSelectedModel.Right);
                        if (selected1.Count() > 0)
                        {
                            var point = selected1.OrderBy(x => x).Last();
                            movement.Add(CurrentSelectedModel.Right - point);
                        }
                        var selected2 = snapX.Where(n => n < CurrentSelectedModel.X);
                        if (selected2.Count() > 0)
                        {
                            var point = selected2.OrderBy(x => x).Last();
                            movement.Add((int)CurrentSelectedModel.X - point);
                        }
                        if (movement.Count > 0)
                        {   
                            CurrentSelectedModel.X -= movement.OrderBy(x => x).First();
                        }
                    }
                    break;
                case "ArrowUp":
                    {
                        if (CurrentSelectedModel.Y <= 0)
                            break;

                        var selected1 = snapY.Where(n => n < CurrentSelectedModel.Bottom);
                        if (selected1.Count() > 0)
                        {
                            var point = selected1.OrderBy(x => x).Last();
                            //이동거리를 추가한다.
                            movement.Add(CurrentSelectedModel.Bottom-point);
                        }
                        var selected2 = snapY.Where(n => n < CurrentSelectedModel.Y);
                        if (selected2.Count() > 0)
                        {
                            var point = selected2.OrderBy(x => x).Last();
                            movement.Add((int)CurrentSelectedModel.Y - point);
                        }

                        if (movement.Count > 0)
                        {  
                            CurrentSelectedModel.Y -= movement.OrderBy(x => x).First();
                        }
                    }
                    break;
            }
        }
        else
        {
            switch (e.Key)
            {
                case "ArrowRight":
                    CurrentSelectedModel.X += 1;
                    break;
                case "ArrowDown":
                    CurrentSelectedModel.Y += 1;
                    break;
                case "ArrowLeft":
                    CurrentSelectedModel.X -= 1;
                    break;
                case "ArrowUp":
                    CurrentSelectedModel.Y -= 1;
                    break;
            }
        }
    }
}
```

</details>
