---
sidebar_position: 5
---

# 支持动态表单和动态Table

对于简单但是占用Html非常大的List页面或者From页面。
我们封装了动态JSON的方式来构建页面。

该功能后期将会在后期可能会直接通过后端JSON文件来统一渲染前端，适用于ERP系统。

```ts
SFSchema = {
    properties: {
      name: {
        type: "string",
        title: "名称",
      },
    },
    required: ["name"],
    ui: {
      spanLabel: 6,
      spanControl: 18,
    },
  };

Array<STColumn<InvoiceListBriefFragment>> = [
    { type: "checkbox", fixed: "left", className: "text-center" },
    {
      title: "名称",
      index: "name",
      className: "text-center",
    },
    {
      title: "操作",
      className: "text-center",
      buttons: [
        {
          icon: "edit",
          text: "编辑",
          type: "static",
          click: item => this.router.navigate(["edit", item.id], { relativeTo: this.route }),
          iifBehavior: "disabled",
        },
        {
          text: "删除",
          icon: "delete",
          type: "del",
          pop: {
            title: "确定删除?",
            okType: "primary",
            icon: "question-circle",
          },
          click: item => this.deleteItem(item.id),
          iifBehavior: "disabled",
        },
      ],
    },
  ];
```
