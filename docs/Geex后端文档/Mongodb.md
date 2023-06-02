---
sidebar_position: 11
---

# MongoDb操作

基于我们自己封装的Geex.MongoDB库，我们可以使用和EF近似的语法来完成数据的CRUD.

业务在开始前会自动开启事务，并在完成提交事务。如果中间需要可以手动save
```cs
//添加数据
DbContext.Attach(entity);

//查询数据
var entity = await DbContext.Queryable<xxxx>()

//删除数据
await xxxList.DeleteAsync();

//save
await xxx.SaveAsync();
```
