---
sidebar_position: 2
---

# 封装compoent基类

为了方便模态框的使用，我们封装了modalHelper。来简化弹框操作。

```ts
 this.modalHelper.createStatic(AddOrEditComponent, { id }).subscribe(async res => {
      if (res) {
        this.msgSrv.success("保存成功");
        this.refresh();
      }
    });
```
