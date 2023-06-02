---
sidebar_position: 10
---

# 工作单元


工作单元的单独使用示例

```cs
using var childScope = serviceProvider.CreateScope();
using var childUow = childScope.ServiceProvider.GetRequiredService<IUnitOfWork>();
foreach (var .... in ...)
{
    try
    {
        //......
        await childUow.CommitAsync();
    }
    catch (Exception e)
    {
        //......
    }
}

```
