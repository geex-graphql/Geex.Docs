---
sidebar_position: 5
---

# 封装compoent基类

为了方便开发，我们针对前端的List页面、编辑页面、弹框页面封装了一套基类。
主要用于将组件通用服务和流程进行封装，只暴露基本的函数，提供实现即可完成业务。

```ts
extends RoutedComponent<invoiceListParams, ListDataContext<InvoiceListBriefFragment>> {

extends BusinessComponentBase {

```
